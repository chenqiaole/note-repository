LeetCode算法笔记

## 前置知识

## 1、二进制减法



![image-20210424153822695](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424153823.png)

![image-20210424153836275](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424153836.png)



## 2、位运算基本知识

![image-20210424154507415](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424154507.png)

#### 2.1 异或运算的规律

> 异或的性质
> 两个数字异或的结果a^b是将 a 和 b 的二进制每一位进行运算，得出的数字。 运算的逻辑是
> 如果同一位的数字相同则为 0，不同则为 1
>
> 异或的规律
>
> 任何数和本身异或则为 0
>
> 任何数和 0 异或是 本身
>
> 异或满足交换律。 即 a ^ b ^ c ，等价于 a ^ c ^ b

![image-20210430153257120](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430153257.png)

#### 2.2 运算优先级

![image-20210502114752130](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502114752.png)



#### 2.3 高级运算

> 详细解释
> 使用整型中各个位来表示状态，比如一行 8 个格子，我们使用 8 位来表示是否放置棋子
> 如： 0000 1111 表示后 4 位都有棋子
>
> 位运算：
> 21. x | y 按位或，对每一位来说，x 和 y 中只要有一边有 1 就是 1
> 如： 1100 0000 | 0000 0011 --> 1100 0011
>
> x & y 按位与，对每一位来说，x 和 y 中必须都是 1 才是 1
> 如： 1100 1111 & 0011 1111 --> 0000 1111
>
> ~x 按位取反，对每一位来说，0 变 1， 1 变 0
> 如： 1111 0000 --> 0000 1111
>
> x << n 左移，用来将一个数的各二进制位全部左移 n 位，右补 0
> 如： 1111 0000 << 2 --> 1100 0000
>
> x >> n 右移，用来将一个数的各二进制位全部右移 n 位，无符号数左补 0
> 如： 1111 0000 >> 2 --> 0011 1100
>
> 高级位运算
> 31. x & -x 可以将最后一位的 1 保留，其它位全部为 0
> 如： 0000 1100 --> 0000 0100
>
> x & (x - 1) 可以将最后一位 1 变成 0
> 如： 0000 1100 --> 0000 1000
>
> 以此特点，我们可以将所有可能的棋子表示为 1111 1111，然后使用 x & -x ，选择最右边的一个
> 如： 1111 1111 --> 0000 0001
>
> 进行计算之后，在回溯的时候，对所有可能的棋子状态使用 x & (x - 1)，这样就排除了本次选择的那一个
> 如： 1111 1111 --> 1111 1110
>
> 如此循环即可将所有可能的棋子都选择进入尝试
> 接下来在 dfs 的时候，由于已选的棋子会产生不可使用的格子，要把这个状态传进去
> 使用 col 表示这一竖行
> 使用 ld 和 rd 表示左斜和右斜，注意这个标志只表示下一行中被斜线穿过的格子
> 在图形上是向左 / 右下一格，因为按行回溯，传递到下一层的是下一行的状态
> 那么对于当前行选中的格子，下一行就是其向左 / 右移动的那一格
> 即：(ld | pick[row]) << 1 和 (rd | pick[row]) >> 1
> 最后，就是在命中的时候，把 pick 中保存的状态转换出来存成字符串
>
> 作者：ikaruga
> 链接：https://leetcode-cn.com/problems/n-queens/solution/51-by-ikaruga/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

##### 2.3.1    x&=(x-1)

![image-20210522030427487](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522030429.png)

## 3、compareTo

> ```java
> // 自定义比较器，实现 Comparator 接口中的 compare() 方法
> public interface Comparator<T> {
>     int compare(T o1, T o2)
> }
> 
> // 查看 Arrays.sort 源码
> public static <T> void sort(T[] a, Comparator<? super T> c) {
>     if (c == null) {
>         sort(a);
>     } else {
>         // 到这进入 LegacyMergeSort
>         if (LegacyMergeSort.userRequested)
>             legacyMergeSort(a, c);
>         else
>             TimSort.sort(a, 0, a.length, c, null, 0, 0);
>     }
> }
> 
> private static <T> void legacyMergeSort(T[] a, Comparator<? super T> c) {
>     T[] aux = a.clone();
>     if (c==null)
>         mergeSort(aux, a, 0, a.length, 0);
>     else
>         // 进行跳到这，进入mergeSort
>         mergeSort(aux, a, 0, a.length, 0, c);
> }
> 
> @SuppressWarnings({"rawtypes", "unchecked"})
> private static void mergeSort(Object[] src,
>                               Object[] dest,
>                               int low, int high, int off,
>                               Comparator c) {
>     int length = high - low;
> 
>     // 比较多只看核心代码就好
>     if (length < INSERTIONSORT_THRESHOLD) {
>         for (int i=low; i<high; i++)
>             for (int j=i; j>low && c.compare(dest[j-1], dest[j])>0; j--)
>                 // 当 compare 方法返回值 > 0 时，就会将数组的前一个数和后一个数做交换
>                 // dest[j-1] 和 dest[j]，说明对于 compare(T o1, T o2) 方法，
>                 // 如果返回值 > 0，那么它会交换传入参数的在数组中的顺序
>                 swap(dest, j, j-1);
>         return;
>     }
>     ...   
> }
> 
> // 再来看这里实现一个比较器，比较规则为：x + y < y + x --> x < y
> // 常规写法
> Arrays.sort(strs, new Comparator<String>(){
>             @Override
>     		// c.compare(dest[j-1], dest[j])>0 
>             public int compare(String str1, String str2){
>                 // str1 < str2 从小到大排
>                 // str1 + str2 <= str2 + str1 ==> 小于0 不会交换 从小到大 
>                 // str1 + str2 > str2 + str1 ==>  大于0，说明 str1 > str2，交换str1 和 str2 的顺序，实现从小到大排序
>                 return (str1 + str2).compareTo(str2 + str1);
>             }
>         });
> 
> // 简化，lambda表达式
> // 简言之：x + y > y + x ==> x > y ==> 返回值 > 0 ==> 交换 x，y 的顺序
> // 	 反之，x + y <= y + x ==> x <= y ==> 返回值 <= 0 ==> 不用交换 x，y 的顺序
> // 实现自定义排序
> Arrays.sort(strs, (x, y) -> (x+y).compareTo(y+x));
> ```



## 4、LinkedList API

![image-20210501172541986](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501172543.png)



## 5、堆排序



![image-20210504153853228](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504153853.png)

![image-20210504153934088](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504153934.png)



![image-20210504154056579](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504154056.png)

2、**第一个非叶子结点的下标是 nums.Length / 2 - 1**





















# 算法部分

## *1、两数相加

![image-20210110023652975](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110023654.png)

### *思路*

#### （1）遍历链表

> 1. 将两个链表看成是相同长度的进行遍历，如果一个链表较短则在前面补 00，比如 987 + 23 = 987 + 023 = 1010
> 2. 每一位计算的同时需要考虑上一位的进位问题，而当前位计算结束后同样需要更新进位值
> 3. 如果两个链表全部遍历完毕后，进位值为 11，则在新链表最前方添加节点 11
> 4. 小技巧：对于链表问题，返回结果为头结点时，通常需要先初始化一个预先指针 pre，该指针的下一个节点指向真正的头结点head。使用预先指针的目的在于链表初始化时无可用节点值，而且链表构造过程需要指针移动，进而会导致头指针丢失，无法返回结果。

![image-20210110025255356](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110025255.png)

```java
public class TwoNumbersAdd {
    
    static class ListNode {
        int val;
        ListNode next;
        
        public ListNode(int x) {
            val = x;
        }
    }
    
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode pre = new ListNode(0);
        ListNode cur = pre;
        int carry = 0;
        while (l1 != null || l2 != null) {
            int val1 = l1 == null ? 0 : l1.val;
            int val2 = l2 == null ? 0 : l2.val;
            int sum = val1 + val2 + carry;
            //当前值
            carry = sum / 10;
            //要写入链表的值
            sum = sum % 10;
            cur.next = new ListNode(sum);
            //移动cur
            cur = cur.next;
            if (l1 != null) {
                l1 = l1.next;
            }
            if (l2 != null) {
                l2 = l2.next;
            }
        }
        if (carry == 1) {
            //最高位相加如果还要进一的话，就再new个结点出来存放这个1
            cur.next = new ListNode(1);
        }
        return pre.next;
    }
}
```

###### 复杂度分析

![image-20210517051840187](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517051841.png)

## *2、[无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331072106.png)



![image-20210110184426031](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110184427.png)

![图片.png](https://pic.leetcode-cn.com/8b7cac826e572c65f8b77e0f380eaa93ab665857a8e916bc4ea36b7765eafc55-%E5%9B%BE%E7%89%87.png)

### 思路

#### （1）滑动窗口

> 这道题主要用到思路是：滑动窗口
>
> 什么是滑动窗口？
>
> 其实就是一个队列,比如例题中的 abcabcbb，进入这个队列（窗口）为 abc 满足题目要求，当再进入 a，队列变成了 abca，这时候不满足要求。所以，我们要移动这个队列！
>
> 如何移动？
>
> 我们只要把队列的左边的元素移出就行了，直到满足题目要求！
>
> 一直维持这样的队列，找出队列出现最长的长度时候，求出解！
>
> 总体思路： 用哈希表保存当前滑动窗口窗口的元素（保存每个元素索引）， 如果窗口的下一个元素已经在哈希表中，则将窗口的起点重置为哈希表中重复元素的下一个索引（跳过重复元素）。每一次迭代都通过max函数比较选出滑动窗口的最大长度。
>
> 时间复杂度：O(n)O(n)

![image-20210110185408813](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110185409.png)



![image.png](https://pic.leetcode-cn.com/c759738650c417a23b1e9636f6f40a553d9258f1cc8907d8da3555b5d2f93e60-image.png)

![image-20210517054628881](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517054629.png)

```java
start = max(table.get(v) + 1, start)
```

这段代码是用来规避哈希表查询到在滑动窗口左边的重复字符的。例如，在"tmmzuxt"这个字符串中，遍历到最后一步时，最后一个字符't'和第一个字符't'是相等的。 如果没有max函数锁定住滑动窗口的最新的左边界，start就会弹回去第一个't'的索引0处，最后输出的最长无重复子串会变成"mmzuxt"。

###### 代码

```java
public int lengthOfLongestSubstring(String s) {
        HashMap<Character, Integer> map = new HashMap<>();
        int maxLen = 0;//用于记录最大不重复子串的长度
        int left = 0;//滑动窗口左指针
        for (int i = 0; i < s.length() ; i++)
        {
            /**
            1、首先，判断当前字符是否包含在map中，如果不包含，将该字符添加到map（字符，字符在数组下标）,
             此时没有出现重复的字符，左指针不需要变化。此时不重复子串的长度为：i-left+1，与原来的maxLen比较，取最大值；

            2、如果当前字符 ch 包含在 map中，此时有2类情况：
             1）当前字符包含在当前有效的子段中，如：abca，当我们遍历到第二个a，当前有效最长子段是 abc，我们又遍历到a，
             那么此时更新 left 为 map.get(a)+1=1，当前有效子段更新为 bca；
             2）当前字符不包含在当前最长有效子段中，如：abba，我们先添加a,b进map，此时left=0，我们再添加b，发现map中包含b，
             而且b包含在最长有效子段中，就是1）的情况，我们更新 left=map.get(b)+1=2，此时子段更新为 b，而且map中仍然包含a，map.get(a)=0；注意这个时候的left是在2的位置
             随后，我们遍历到a，发现a包含在map中，且map.get(a)=0，如果我们像1）一样处理，就会发现 left=map.get(a)+1=1，left回溯了，字段变成bba了，出问题了，实际上，left此时
             应该不变，left还应该为2，子段变成 ba才对。

             为了处理以上2类情况，我们每次更新left，left=Math.max(left , map.get(ch)+1).
             另外，更新left后，不管原来的 s.charAt(i) 是否在最长子段中，我们都要将 s.charAt(i) 的位置更新为当前的i，
             因此此时新的 s.charAt(i) 已经进入到 当前最长的子段中！
             */
            if(map.containsKey(s.charAt(i)))
            {
                //这个是有重复的时候才移动左边界
                left = Math.max(left , map.get(s.charAt(i))+1);
            }
            //不管是否更新left，都要更新 s.charAt(i) 的位置！
            map.put(s.charAt(i) , i);
            //这个是每个遍历的时候都要计算
            maxLen = Math.max(maxLen , i-left+1);
        }
        
        return maxLen;
    }
```

###### 复杂度分析

![image-20210517060724347](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517060725.png)

![image-20210517060743181](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517060744.png)

#### （2）队列解法

![image-20210110194919624](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110194920.png)

```java
    public int lengthOfLongestSubstring(String s) {
        //用链表实现队列，队列是先进先出的
        Queue<Character> queue = new LinkedList<>();
        int res = 0;
        for (char c : s.toCharArray()) {
            //注意这里是while，不是if，有重复的话，一直出队
            while (queue.contains(c)) {
                //如果有重复的，队头出队
                queue.poll();
            }
            //添加到队尾
            queue.add(c);
            res = Math.max(res, queue.size());
        }
        return res;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/solution/javade-6chong-jie-fa-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210517061106925](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517061107.png)

![image-20210110204129560](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110204130.png)

## *3、[整数反转](https://leetcode-cn.com/problems/reverse-integer/)

![image-20210110210537372](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110210537.png)

### 思路

#### （1）数学取余

本题如果不考虑溢出问题，是非常简单的。解决溢出问题有两个思路，第一个思路是通过字符串转换加try catch的方式来解决，第二个思路就是通过数学计算来解决。
由于字符串转换的效率较低且使用较多库函数，所以解题方案不考虑该方法，而是通过数学计算来解决。

首先我们想一下，怎么去反转一个整数？
用栈？
或者把整数变成字符串，再去反转这个字符串？
这两种方式是可以，但并不好。实际上我们只要能拿到这个整数的 末尾数字 就可以了。
以12345为例，先拿到5，再拿到4，之后是3，2，1，我们按这样的顺序就可以反向拼接处一个数字了，也就能达到 反转 的效果。
怎么拿末尾数字呢？好办，用取模运算就可以了

![1.jpg](https://pic.leetcode-cn.com/be35cb60bec9a9ae794abad671e6618abb5664780bc7ee30ca93ca423884a666-1.jpg)

![image-20210110210852104](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110210852.png)

> 通过循环将数字x的每一位拆开，在计算新值时每一步都判断是否溢出。
> 溢出条件有两个，一个是大于整数最大值MAX_VALUE，另一个是小于整数最小值MIN_VALUE，设当前计算结果为ans，下一位为pop。
> 从ans * 10 + pop > MAX_VALUE这个溢出条件来看
> 当出现 ans > MAX_VALUE / 10 且 还有pop需要添加 时，则一定溢出
> 当出现 ans == MAX_VALUE / 10 且 pop > 7 时，则一定溢出，7是2^31 - 1的个位数
> 从ans * 10 + pop < MIN_VALUE这个溢出条件来看
> 当出现 ans < MIN_VALUE / 10 且 还有pop需要添加 时，则一定溢出
> 当出现 ans == MIN_VALUE / 10 且 pop < -8 时，则一定溢出，8是-2^31的个位数

![image-20210110211439329](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110211439.png)

![image-20210110211458418](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210110211458.png)

```java
class Solution {
    public int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            int pop = x % 10;
            if (ans > Integer.MAX_VALUE / 10 || (ans == Integer.MAX_VALUE / 10 && pop > 7)) 
                return 0;
            if (ans < Integer.MIN_VALUE / 10 || (ans == Integer.MIN_VALUE / 10 && pop < -8)) 
                return 0;
            ans = ans * 10 + pop;
            x /= 10;
        }
        return ans;
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/reverse-integer/solution/hua-jie-suan-fa-7-zheng-shu-fan-zhuan-by-guanpengc/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517063442.png)

![image-20210517063809274](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517063810.png)

#### （2）字符串法



![1.jpg](https://pic.leetcode-cn.com/964f121dab19ac39aa7168a4462e2949ea3c84e8a1129d67d241b95ee15b332d-1.jpg)

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0) {
            return false;
        }
        String oldStr = ""+x;
        String newStr = new StringBuilder(""+x).reverse().toString();
        return oldStr.equals(newStr);
    }
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/palindrome-number/solution/chao-xiang-xi-tu-jie-san-chong-jie-fa-9-hui-wen-sh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 反转一半数字

反转整数可能会有益处的风险(虽然结果也是对的)，那我们想想是否能优化一下。【回文】的特点是什么？
首尾是一样的

![4.jpg](https://pic.leetcode-cn.com/4e8d3f95bfcaa212d0baf5c6e7eec56c53a7af6dace8946f3a9ae93817707e62-4.jpg)

![image-20210111002037385](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210111002038.png)

![image-20210111002101192](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210111002101.png)

```java
class Solution {
    public boolean isPalindrome(int x) {
        if(x<0 || (x%10==0 &&x!=0)) {
            return false;
        }
        int ans = 0;
        while(x>ans) {
            ans = ans*10 + x%10;
            x /= 10;
        }
        return x==ans || x==(ans/10);
    }
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/palindrome-number/solution/chao-xiang-xi-tu-jie-san-chong-jie-fa-9-hui-wen-sh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## *4、[最小栈](https://leetcode-cn.com/problems/min-stack/)

![image-20210517064012389](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517064013.png)

### 思路

#### （1）辅助栈



![image-20210517065442852](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517065443.png)



![155.gif](https://pic.leetcode-cn.com/28724fa9f92b6952f7fdaf8760edd1dea850b137c22df28751f1cdd4d2680992-155.gif)

> ![image-20210517070123916](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517070124.png)

```json
入栈 3 
|   |    |   |
|   |    |   |
|_3_|    |_3_|
stack  minStack

入栈 5 ， 5 大于 minStack 栈顶，不处理
|   |    |   |
| 5 |    |   |
|_3_|    |_3_|
stack  minStack

入栈 2 ，此时右边的 minStack 栈顶就保存了当前最小值 2 
| 2 |    |   |
| 5 |    | 2 |
|_3_|    |_3_|
stack  minStack

出栈 2，此时右边的 minStack 栈顶就保存了当前最小值 3
|   |    |   |
| 5 |    |   |
|_3_|    |_3_|
stack  minStack

出栈 5，右边 minStack 不处理
|   |    |   |
|   |    |   |
|_3_|    |_3_|
stack  minStack

出栈 3
|   |    |   |
|   |    |   |
|_ _|    |_ _|
stack  minStack

作者：windliang
链接：https://leetcode-cn.com/problems/min-stack/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-38/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 代码

```java
class MinStack {
    private Stack<Integer> stack;
    private Stack<Integer> min_stack;
    public MinStack() {
        stack = new Stack<>();
        min_stack = new Stack<>();
    }
    public void push(int x) {
        stack.push(x);
        if(min_stack.isEmpty() || x <= min_stack.peek())
            min_stack.push(x);
    }
    public void pop() {
        if(stack.pop().equals(min_stack.peek()))
            min_stack.pop();
    }
    public int top() {
        return stack.peek();
    }
    public int getMin() {
        return min_stack.peek();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/min-stack/solution/min-stack-fu-zhu-stackfa-by-jin407891080/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210517065030799](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517065031.png)

![image-20210517065838742](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517065839.png)

#### （2）一个辅助栈

![image-20210518034707262](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518034708.png)



###### 代码

```java
class MinStack {
    int min = Integer.MAX_VALUE;
    Stack<Integer> stack = new Stack<Integer>();
    public void push(int x) {
        //当前值更小，相等也可以，这样就有两个最小值了，当出掉一个最小的，那另一个还在栈顶，还是最		//小的
        if(x <= min){   
            //将之前的最小值保存
            stack.push(min);
            //更新最小值
            min=x;
        }
        stack.push(x);
    }

    public void pop() {
        //如果弹出的值是最小值，那么将下一个元素更新为最小值
        if(stack.pop() == min) {
            min=stack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return min;
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/min-stack/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-38/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210518034548487](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518034549.png)

#### （3）差值栈

![image-20210518035705455](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518035705.png)

```json
入栈 3，存入 3 - 3 = 0
|   |   min = 3
|   |     
|_0_|    
stack   

入栈 5，存入 5 - 3 = 2，差值是正数，说明当前值都比最小值大，不更新min
|   |   min = 3
| 2 |     
|_0_|    
stack  

入栈 2，因为出现了更小的数，所以我们会存入一个负数，这里很关键
也就是存入  2 - 3 = -1, 并且更新 min = 2 
对于之前的 min 值 3, 我们只需要用更新后的 min - 栈顶元素 -1 就可以得到    
| -1|   min = 2
| 5 |     
|_3_|    
stack  

入栈 6，存入  6 - 2 = 4
| 4 |   min = 2
| -1| 
| 5 |     
|_3_|    
stack  

出栈，返回的值就是栈顶元素 4 加上 min，就是 6
|   |   min = 2
| -1| 
| 5 |     
|_3_|    
stack  

出栈，此时栈顶元素是负数，说明之前对 min 值进行了更新。
入栈元素 - min = 栈顶元素，入栈元素其实就是当前的 min 值 2
所以更新前的 min 就等于入栈元素 2 - 栈顶元素(-1) = 3
|   | min = 3
| 5 |     
|_3_|    
stack     

作者：windliang
链接：https://leetcode-cn.com/problems/min-stack/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-38/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210518035830048](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518035830.png)

###### 代码

```java
public class MinStack {
    long min;
	Stack<Long> stack;

	public MinStack(){
        stack=new Stack<>();
    }

	public void push(int x) {
		if (stack.isEmpty()) {
			min = x;
			stack.push(x - min);
		} else {
			stack.push(x - min);
			if (x < min){
				min = x; // 更新最小值
			}
				
		}
	}

	public void pop() {
		if (stack.isEmpty())
			return;

		long pop = stack.pop();
		
		//弹出的是负值，要更新 min
		if (pop < 0) {
			min = min - pop;
		}

	}

	public int top() {
		long top = stack.peek();
		//负数的话，出栈的值保存在 min 中
		if (top < 0) {
			return (int) (min);
        //出栈元素加上最小值即可
		} else {
			return (int) (top + min);
		}
	}

	public int getMin() {
		return (int) min;
	}
}

作者：windliang
链接：https://leetcode-cn.com/problems/min-stack/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-38/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210518035859888](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518035900.png)

#### （4）自定义链表

![image-20210518040205621](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518040205.png)



```java
class MinStack {
    class Node{
        int value;
        int min;
        Node next;

        Node(int x, int min){
            this.value=x;
            this.min=min;
            next = null;
        }
    }
    Node head;
    //每次加入的节点放到头部
    public void push(int x) {
        if(null==head){
            head = new Node(x,x);
        }else{
            //当前值和之前头结点的最小值较小的做为当前的 min，这样头结点就可以获取最小值了
            Node n = new Node(x, Math.min(x,head.min));
            //头插法，保证后进的先出
            n.next=head;
            head=n;
        }
    }

    public void pop() {
        if(head!=null)
            head =head.next;
    }

    public int top() {
        if(head!=null)
            return head.value;
        return -1;
    }

    public int getMin() {
        if(null!=head)
            //最小值直接就是头结点获取了
            return head.min;
        return -1;
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/min-stack/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-38/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210518041129576](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518041130.png)



## *5、[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

![image-20210111020229277](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210111020230.png)

### 思路1

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519204457.png)





![image-20210111020822410](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210111020822.png)

**一直是左括号入栈，突然来个右括号，这个右括号一定要跟栈顶的括号匹配才行，再来一个右括号，这个又要跟栈顶下面那个配对，依次类推**

```java
class Solution {
    private static final Map<Character,Character> map = new HashMap<Character,Character>(){{
        put('{','}'); put('[',']'); put('(',')'); put('?','?');
    }};
    public boolean isValid(String s) {
        if(s.length() > 0 && !map.containsKey(s.charAt(0))) return false;
        LinkedList<Character> stack = new LinkedList<Character>() {{ add('?'); }};
        for(Character c : s.toCharArray()){
            if(map.containsKey(c)) stack.addLast(c);
            else if(map.get(stack.removeLast()) != c) return false;
        }
        //如果整个字符串都遍历完，到这里 栈中就剩一个问号了
        return stack.size() == 1;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/valid-parentheses/solution/valid-parentheses-fu-zhu-zhan-fa-by-jin407891080/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519211112961](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519211113.png)

![image-20210519211129748](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519211130.png)

### 思路2

> 特判：过滤空字符串
> 创建一个辅助栈：
> 遍历，对每一个字符进行如下操作：
> 若为左括号，则往栈中存放右括号
> 若为右括号，如果 栈为空 或者 该右括号与取出的栈顶元素不一样，则返回false;
> 返回栈是否为空的状态；
>
> 作者：xmblgt
> 链接：https://leetcode-cn.com/problems/valid-parentheses/solution/yi-ci-bian-li-si-lu-chao-ji-jian-dan-bu-dian-zan-b/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```java
class Solution {
    public boolean isValid(String s) {
        //1.特判
        if(s.isEmpty()) return true;
        //2.创建辅助栈
        Stack<Character> stack = new Stack<>();
        //3.遍历
        for(char c : s.toCharArray()){
            if(c == '('){
                stack.push(')');
            }else if(c == '['){
                stack.push(']');
            }else if(c == '{'){
                stack.push('}');
            }else if(stack.isEmpty() || c != stack.pop()){
                return false;
            }
        }
        //4.返回
        return stack.isEmpty();
    }
}

作者：xmblgt
链接：https://leetcode-cn.com/problems/valid-parentheses/solution/yi-ci-bian-li-si-lu-chao-ji-jian-dan-bu-dian-zan-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## *6、[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

![image-20210519211352591](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519211353.png)



![image-20210519211419384](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519211420.png)

### 思路

#### （1）栈







![1.gif](https://pic.leetcode-cn.com/47fff35dd3fd640ba60349c78b85242ae8f4b850f06a282cd7e92c91e6eff406-1.gif)



```java
class Solution {
	public List<Integer> inorderTraversal(TreeNode root) {
		List<Integer> res = new ArrayList<Integer>();
		Stack<TreeNode> stack = new Stack<TreeNode>();
		while(stack.size()>0 || root!=null) {
			//不断往左子树方向走，每走一次就将当前节点保存到栈中
			//这是模拟递归的调用
			if(root!=null) {
				stack.add(root);
				root = root.left;
			//当前节点为空，说明左边走到头了，从栈中弹出节点并保存
			//然后转向右边节点，继续上面整个过程
			} else {
				TreeNode tmp = stack.pop();
				res.add(tmp.val);
				root = tmp.right;
			}
		}
		return res;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/binary-tree-inorder-traversal/solution/dong-hua-yan-shi-94-er-cha-shu-de-zhong-xu-bian-li/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519214034662](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519214034.png)

![image-20210519213800339](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519213800.png)

## *7、[接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

![image-20210519214214040](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519214214.png)



### 思路

#### 单调栈

> 这里我们也系统的说一下单调栈，单调栈含义就是栈内的元素是单调的，我们这两个题目用到的都是递减栈（相同也可以），我们依次将元素压入栈，如果当前元素小于等于栈顶元素则入栈，如果大于栈顶元素则先将栈顶不断出栈，直到当前元素小于或等于栈顶元素为止，然后再将当前元素入栈。就比如下图的4，想入栈的话则需要2，3出栈之后才能入栈，因为4大于他俩。

![image-20210112030913246](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210112030913.png)



我们了解单调栈的含义下面我们来看一下接雨水问题到底该怎么做，其实原理也很简单，我们通过我们的例3来进行说明。

首先我们依次入栈4，3，2，0我们的数组前四个元素是符合单调栈规则的。但是我们的第五个1，是大于0的。那我们就需要0出栈1入栈。但是我们这样做是为了什么呢？有什么意义呢？别急我们来看下图。

![xxx](https://pic.leetcode-cn.com/1609654701-uvYHjT-file_1609654702874)

上图我们的，4，3，2，0已经入栈了，我们的另一个元素为1，栈顶元素为0，栈顶下的元素为2。那么我们在这一层接到的雨水数量怎么算呢？2，0，1这三个元素可以接住的水为一个单位(见下图)这是我们第一层接到水的数量。

注：能接到水的情况，肯定是中间低两边高，这样才可以。

![image-20201112170019593](https://pic.leetcode-cn.com/1609909762-syoSXm-file_1609909762438)


因为我们需要维护一个单调栈，所以我们则需要将0出栈1入栈，那么此时栈内元素为4，3，2，1。下一位元素为1，我们入栈，此时栈内元素为4，3，2，1，1。下一元素为5，栈顶元素为1，栈顶的下一元素仍为1，则需要再下一个元素，为2，那我们求当前层接到的水的数量。

![image-20201112171354547](https://pic.leetcode-cn.com/1609654701-yqZhzM-file_1609654702866)

我们是通过2，1，1，5这四个元素求得第二层的接水数为1*3=3;1是因为min(2-1,5-1)=min(1,4)得来的，大家可以思考一下木桶效应。装水的多少，肯定是按最短的那个木板来的，所以高度为1，3的话是因为5的索引为6，2的索引为2，他们之间共有三个元素（3，4，5）也就是3个单位。所以为6-2-1=3。

将1出栈之后，我们栈顶元素就变成了2，下一元素变成了3，那么3，2，5这三个元素同样也可以接到水。

![image-20210112031316212](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210112031316.png)

这是第三层的接水情况，能够接到4个单位的水，下面我们继续出栈2，那么我们的4，3，5仍然可以接到水啊。

![image-20210112031659539](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210112031659.png)

这是我们第四层接水的情况，一共能够接到5个单位的水，那么我们总的接水数加起来，那就是

1+3+4+5=13。

作者：tan45du
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/yi-miao-jiu-neng-du-dong-de-dong-hua-jie-o9sv/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



#### （2）按列求

![image-20210519231657468](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519231657.png)

![image-20210519230300472](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519230300.png)

![image-20210519230552333](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519230552.png)

![image-20210519230655646](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519230655.png)

![image-20210519230732766](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519230733.png)



###### 代码

```java
public int trap(int[] height) {
    int sum = 0;
    //对于每一列能装多少水，只跟两边的最高高度有关，所以要各自找两边的最高高度
    //最两端的列不用考虑，因为一定不会有水。所以下标从 1 到 length - 2
    for (int i = 1; i < height.length - 1; i++) {
        int max_left = 0;
        //找出左边最高
        for (int j = i - 1; j >= 0; j--) {
            if (height[j] > max_left) {
                max_left = height[j];
            }
        }
        int max_right = 0;
        //找出右边最高
        for (int j = i + 1; j < height.length; j++) {
            if (height[j] > max_right) {
                max_right = height[j];
            }
        }
        //找出两端较小的
        int min = Math.min(max_left, max_right);
        //只有较小的一段大于当前列的高度才会有水，其他情况不会有水
        if (min > height[i]) {
            sum = sum + (min - height[i]);
        }
    }
    return sum;
}

作者：windliang
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519232556176](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519232556.png)

![image-20210519232613988](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20210519232613988.png)



#### （3）栈

![image-20210519222810234](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519222810.png)

![image-20210519224356270](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224356.png)

![image-20210519224527254](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224528.png)

![image-20210519224613146](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224613.png)

![image-20210519224634209](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224634.png)





![image-20210112025047167](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210112025048.png)









![image-20210519224307446](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224307.png)

![image-20210519224324207](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519224324.png)

###### 代码



```java
public int trap6(int[] height) {
    int sum = 0;
    Stack<Integer> stack = new Stack<>();
    int current = 0;
    while (current < height.length) {
        //如果栈不空并且当前指向的高度大于栈顶高度就一直循环
        //当前元素大于栈顶元素，，说明这个时候可以在栈顶装水了，因为栈顶的两边都比它高，相当于
        //木桶的两个壁
        while (!stack.empty() && height[current] > height[stack.peek()]) {
            //先求出桶底的高度，就是栈顶位置了
            int h = height[stack.peek()]; //取出要出栈的元素
            stack.pop(); //出栈
            //左边没有壁了，那就放不了水了
            if (stack.empty()) { // 栈空就出去
                break; 
            }
            //这个是算桶的两个壁距离，不算边界，所以-1
            int distance = current - stack.peek() - 1; //两堵墙之前的距离。
            //这个是获取桶的短边，水只能装到这里
            int min = Math.min(height[stack.peek()], height[current]);
            //跟作为桶底的位置的高度差
            sum = sum + distance * (min - h);
        }
        stack.push(current); //当前指向的墙入栈
        current++; //指针后移
    }
    return sum;
}

作者：windliang
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    public int trap(int[] height) {
         Stack<Integer> stack = new Stack<Integer>();
         
         int water = 0;       
         //特殊情况       
         if(height.length <3){
             return 0;
         }       
         for(int i = 0; i < height.length; i++){
             while(!stack.isEmpty() && height[i] > height[stack.peek()]){          
                 //栈顶元素            
                 int popnum = stack.pop();           
                 //相同元素的情况例1，1          
                 while(!stack.isEmpty()&&height[popnum] == height[stack.peek()]){
                     stack.pop();
                 }           
                 //计算该层的水的单位           
                 if(!stack.isEmpty()){
                     int temp = height[stack.peek()];//栈顶元素值              
                     //高                     
                     int hig = Math.min(temp-height[popnum],height[i]-height[popnum]);                    
                     //宽                   
                     int wid = i-stack.peek()-1;
                     water +=hig * wid;
                 }

             }             
             //这里入栈的是索引          
             stack.push(i);
         }
         return water;
    }
}

作者：tan45du
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/yi-miao-jiu-neng-du-dong-de-dong-hua-jie-o9sv/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519225638976](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519225639.png)

![image-20210519225704582](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519225704.png)

#### （4）动态规划

![image-20210520005004306](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520005004.png)

###### 代码



```java
public int trap(int[] height) {
    int sum = 0;
    int[] max_left = new int[height.length];
    int[] max_right = new int[height.length];
    
    for (int i = 1; i < height.length - 1; i++) {
        //这里的max_left[i-1]是不包括i-1这个位置的，所以应该是i-1位置左边的最大的高度
        max_left[i] = Math.max(max_left[i - 1], height[i - 1]);
    }
    for (int i = height.length - 2; i >= 0; i--) {
        max_right[i] = Math.max(max_right[i + 1], height[i + 1]);
    }
    for (int i = 1; i < height.length - 1; i++) {
        int min = Math.min(max_left[i], max_right[i]);
        if (min > height[i]) {
            sum = sum + (min - height[i]);
        }
    }
    return sum;
}

作者：windliang
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210520005115772](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520005116.png)

![image-20210520005905758](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520005906.png)



#### （5）双指针

![image-20210520020447832](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520020448.png)

![image-20210520020628288](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520020628.png)



![image-20210520020713655](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520020714.png)

![image-20210520021209139](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520021209.png)





###### 代码

```java
public int trap(int[] height) {
    int sum = 0;
    int max_left = 0;
    int max_right = 0;
    int left = 1;
    int right = height.length - 2; // 加右指针进去，从两边往中间遍历
    //i只是控制遍历的次数而已，真正的指针是left和right来指向要遍历的值的
    //所以，下面取当前的高度都没有用到i，都是用left和right
    for (int i = 1; i < height.length - 1; i++) {
        //从左到右更
        if (height[left - 1] < height[right + 1]) {
            max_left = Math.max(max_left, height[left - 1]);
            int min = max_left;
            if (min > height[left]) {
                sum = sum + (min - height[left]);
            }
            left++;
        //从右到左更
        } else {
            max_right = Math.max(max_right, height[right + 1]);
            int min = max_right;
            if (min > height[right]) {
                sum = sum + (min - height[right]);
            }
            right--;
        }
    }
    return sum;
}

作者：windliang
链接：https://leetcode-cn.com/problems/trapping-rain-water/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210520022032567](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520022032.png)

![image-20210520022112789](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520022113.png)

## *8、[合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

![image-20210519232657399](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519232658.png)

### 思路

#### （1）递归

![640.gif](https://pic.leetcode-cn.com/1605953062-RMAqlu-640.gif)

递归的两个条件：

终止条件：当l1或者l2中有一个为空时，就返回
函数内要做什么：如果l1.val小于等于l2.val，那么将l1.next指向 l1的后续节点和l2中较小的一个
终止条件比较好理解，函数内做的事情就不那么好懂了 l1的后续节点和l2中较小的一个

l1.val<=l2.val后，我们就能确定一个较小的节点了，比如是l1。因为链表还没结束，我们要继续比较，这时候比较的是l1.next和l2。
l1.next和l2是在下一层递归函数中处理的，再下一层函数中，也会确定一个较小的节点，我们将这个较小的节点记做x，然后上一层的l1.next就指向这个x节点。
后面就是不断的递归，最终把整个链表给串起来。

![image-20210116231824275](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210116231825.png)

![img](https://pic.leetcode-cn.com/1605953131-EEWFLE-2.jpg)

![img](https://pic.leetcode-cn.com/1605953137-glsFjU-3.jpg)

![img](https://pic.leetcode-cn.com/1605953142-vNGfXC-4.jpg)

![img](https://pic.leetcode-cn.com/1605953147-lfoXLo-5.jpg)

![img](https://pic.leetcode-cn.com/1605953153-zWjLbj-6.jpg)

![img](https://pic.leetcode-cn.com/1605953159-elLWYs-7.jpg)

![img](https://pic.leetcode-cn.com/1605953164-IXClBR-8.jpg)

![img](https://pic.leetcode-cn.com/1605953171-hyWheC-9.jpg)

![img](https://pic.leetcode-cn.com/1605953181-SSbOro-10.jpg)

![img](https://pic.leetcode-cn.com/1605953188-cwnNjs-11.jpg)

上图中第一步、第二步：当链表1的节点1 <= 链表2的节点1时候，让链表1的节点1.next指向下一层递归函数，但此时程序并没有执行完，链表1的节点1.next指向的还是一个未知的节点，到底会指向谁，需要等后面的递归函数执行完返回后才知道
第三步、第四步：链表1的节点2>链表2的节点1，让链表2的节点1.next指向下一层递归函数，具体指向谁需要等后面的递归函数执行完返回后才知道
第五步、第六步：链表1的节点2<=链表2的节点3，让链表1的节点2.next 指向下一层递归函数
第七步、第八步：链表2的节点3.next 指向下一层的递归函数
第九步、第十步：链表1的节点4.next 指向下一层递归函数
经过上面 10 步之后，递归调用就到头了，因为上图中最后一张图，最右边的黑色方框里只剩下一个4了。此时就会触发递归终止条件，然后不断返回



作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/chao-xiang-xi-tu-jie-di-gui-zhi-xing-guo-cheng-21h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



![img](https://pic.leetcode-cn.com/1605953273-BPXBQU-12.jpg)

![img](https://pic.leetcode-cn.com/1605953283-bRPUJl-13.jpg)

![img](https://pic.leetcode-cn.com/1605953290-QKiipt-14.jpg)

![img](https://pic.leetcode-cn.com/1605953296-FgZhnQ-15.jpg)

![img](https://pic.leetcode-cn.com/1605953303-MzSKgr-16.jpg)

![img](https://pic.leetcode-cn.com/1605953312-clwXGW-17.jpg)

![img](https://pic.leetcode-cn.com/1605953317-ZenBdK-18.jpg)

![img](https://pic.leetcode-cn.com/1605953324-RShRTP-19.jpg)

![img](https://pic.leetcode-cn.com/1605953331-CiyUqI-20.jpg)

![img](https://pic.leetcode-cn.com/1605953341-NUjtMG-21.jpg)

再来分析一下上图中递归函数是怎么返回的：

第11步：此时链表1为空，于是触发递归终止条件，返回链表2的节点4
第12步：链表2的节点4返回到上一层递归函数，上一层函数是链表1的节点4，于是链表1的节点4.next 就指向 链表2的节点4
第13步：链表1的节点4返回
第14步：链表2的节点4返回到上一层递归函数，上一层函数是链表2的节点3，于是链表2的节点3.next 就指向 链表1的节点4
第15步：链表2的节点3返回
第16步：链表2的节点3返回到上一层递归函数，上一层的函数是链表1的节点2，于是链表1的节点2.next 就指向 链表2的节点3
第17步：链表1的节点2返回
第18步：链表1的节点2返回到上一层递归函数，上一层函数是链表2的节点1，于是链表2的节点1.next 就指向 链表1的节点2
第19步：链表2的节点1返回
第20步：链表2的节点1返回到上一层递归函数，上一层函数是链表的节点1，于是链表1的节点1.next 就指向 链表2的节点1
如下图所示，最后返回链表1的节点1，此时两个链表就被合并成一个链表了

![22.jpg](https://pic.leetcode-cn.com/1605953375-dxQErL-22.jpg)



###### 代码

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        else if (l2 == null) {
            return l1;
        }
        else if (l1.val < l2.val) {
            //直接取当前的l1的结点，然后l1后面的结点继续和l2合并，返回当前的l1为头结点的链表挂在
            //上层的结点后面
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }

    }
}

作者：z1m
链接：https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/yi-kan-jiu-hui-yi-xie-jiu-fei-xiang-jie-di-gui-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210520023433112](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520023433.png)

#### （2）迭代

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode prehead = new ListNode(-1);

        ListNode prev = prehead;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                prev.next = l1;
                l1 = l1.next;
            } else {
                prev.next = l2;
                l2 = l2.next;
            }
            prev = prev.next;
        }

        // 合并后 l1 和 l2 最多只有一个还未被合并完，我们直接将链表末尾指向未合并完的链表即可
        prev.next = l1 == null ? l2 : l1;

        return prehead.next;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/he-bing-liang-ge-you-xu-lian-biao-by-leetcode-solu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210520024321993](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520024322.png)

## *9、[最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

![image-20210119020614062](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210119020614.png)

### 思路

#### （1）暴力破解

枚举所有的子区间：

使用双层循环，穷举所有的子区间；
然后再对子区间内的所有元素求和；
时间复杂度是立方级别的。
参考代码 1：

这里要注意一些边界条件：

变量 i 表示结尾的那个下标；
变量 j 表示从下标 0 依次向前走；

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < len; i++) {
            for (int j = 0; j <= i; j++) {
                int sum = sumOfSubArray(nums, j, i);
                res = Math.max(res, sum);
            }
        }
        return res;
    }

    private int sumOfSubArray(int[] nums, int left, int right) {
        // 子区间的和
        int res = 0;
        for (int i = left; i <= right; i++) {
            res += nums[i];
        }
        return res;
    }

}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

时间复杂度：O(N^3)O(N 
3
 )，这里 NN 为数组的长度；
空间复杂度：O(1)O(1)。
提交以后发现「超时」，有两种情况：

程序当中写了「死循环」；
代码「正确」，复杂度较高，本解法属于这种情况。

优化：事实上，上面的代码有一些重复计算。这是因为相同前缀的区间求和，可以通过类似「状态转移」的方法得到。

例如：计算子区间 [1, 4] 的和可以在计算子区间 [1, 3] 的基础上，再加上 nums[4] 得到。（这里感谢用户 @YYM 的提醒）。

因此，只需要枚举子序的左端点，然后再扫描右端点，就可以减少一个级别的复杂度。

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < len; i++) {
            int sum = 0;
            for (int j = i; j < len; j++) {
                sum += nums[j];
                res = Math.max(res, sum);
            }
        }
        return res;
    }
}
```



时间复杂度：O(N^2)O(N 
2
 )；
空间复杂度：O(1)O(1)。
其实这道题是一个非常经典的动态规划问题。

该问题最早于 1977 年提出，但是直到 1984 年才被 Jay Kadane 发现了线性时间的最优解法。

#### （2）动态规划

于是我们把目光落到动态规划上面来，首先需要把这个问题分解成最优子问题来解。最主要的思路就是将上面的45个组合进行分类，分解成数量较少的几个子问题。在这里我们一共有9个数字，顺理成章的我们把组合分解成9个小组的组合。

第一个子组合是以**第一个数字结尾**的连续序列，也就是[-2]，最大值-2

第二个子组合是以**第二个数字结尾**的连续序列，也就是[-2,1], [1]，最大值1

第三个子组合是以**第三个数字结尾**的连续序列，也就是[-2,1,3], [1,3], [3]，最大值4

以此类推。。。

如果我们能够得到每一个子组合的最优解，也就是子序列的最大值，整体的最大值就可以通过比较这9个子组合的最大值来得到了。现在我们找到了最优子问题，重叠子问题在哪呢？那就得细心比较一下每个子问题。

从第二个子组合和第三个子组合可以看到，组合3只是在组合2的基础上每一个数组后面添加第3个数字，也就是3，然后增加一个只有第三个数字的数组[3]。这样两个组合之间的关系就出现了，可是我们不关心这个序列是怎么生成的，只是关心最大值之间的关系。我们将子组合三分成两种情况：

继承子组合二得到的序列，也就是[-2,1,3], [1,3] （最大值 = 第二个组合的最大值 + 第三个数字）
单独第三个数字的序列，也就是[3] （最大值 = 第三个数字）

如果第二个序列的最大值大于0，那么最大值1就比最大值2要大，反之最大值2较大。这样，我们就通过第二个组合的最大值和第三个数字，就得到了第三个组合的最大值。因为第二个组合的结果被重复用到了，所以符合这个重叠子问题的定义。通俗来讲这个问题就变成了，第i个子组合可以通过第i-1个子组合的最大值和第i个数字获得，如果第i-1个子组合的最大值没法给第i个数字带来正增益，我们就抛弃掉前面的子组合，自己就是最大的了。

**如果Max(i−1)>0,Max(i)=Max(i−1)+Nums(i)**
**如果Max(i−1)<=0,Max(i)=Nums(i)**
	

来看看代码，我们只需要一个变量`subMax`保存前面子组合的最大值，另外一个`max`保存全局最大值。

##### 第 1 步：定义状态

既然一个连续子数组一定要以一个数作为结尾，那么可以将状态定义成如下：

dp[i]：表示以 nums[i] 结尾的连续子数组的最大和。

类似的状态定义还有：「力扣」第 300 题：「最长上升子序列」。

以 「什么什么结尾」这种定义方式是动态规划的无后效性理论的应用，简而言之，确定一件事情，分类讨论才好开展，一个数在子序列中是否被选择，是这个问题的隐含条件。

##### 第 2 步：思考状态转移方程

根据状态的定义，由于 nums[i] 一定会被选取，并且 dp[i] 所表示的连续子序列与 dp[i - 1] 所表示的连续子序列（有可能）就差一个 nums[i] 。

假设数组 nums 全是正数，那么一定有 dp[i] = dp[i - 1] + nums[i]。

在一般情况下 dp[i - 1] 有可能是负数，例如前几个数都是负数，突然来了一个正数。

于是分类讨论：

如果 dp[i - 1] >= 0，那么可以把 nums[i] 直接接在 dp[i - 1] 表示的那个数组的后面；
如果 dp[i - 1] < 0，那么加上前面的数反而越来越小了，于是「另起炉灶」，单独的一个 nums[i]，就是 dp[i]。
以上两种情况的最大值就是 dp[i] 的值，写出如下状态转移方程：

dp[i] = \begin{cases} dp[i - 1] + nums[i], & if \quad dp[i - 1] \ge 0 \\ nums[i], & if \quad dp[i - 1] < 0 \end{cases}
dp[i]={ 
dp[i−1]+nums[i],
nums[i],
	

ifdp[i−1]≥0
ifdp[i−1]<0
	


记为「状态转移方程 1」。

状态转移方程还可以这样写，反正求的是最大值，也不用分类讨论了，就这两种情况，**取最大即可**，因此还可以写出状态转移方程如下：

dp[i] = \max \{nums[i],\; dp[i - 1] + nums[i]\}
dp[i]=max{nums[i],dp[i−1]+nums[i]}

记为「状态转移方程 2」。

动态规划的问题经常要分类讨论，这是因为动态规划的问题本来就有最优子结构的特征，即大问题的最优解通常由小问题的最优解得到，那么我们就需要通过分类讨论，得到大问题的小问题究竟是哪些。

##### 第 3 步：思考初始值

`dp[0]` 根据定义，一定以 `nums[0]` 结尾，因此 `dp[0] = nums[0]`。

##### 第 4 步：思考输出

这里状态的定义不是题目中的问题的定义，不能直接将最后一个状态返回回去。

输出应该是把所有的 dp[0]、dp[1]、……、dp[n - 1] 都看一遍，取最大值。 同样的情况也适用于「力扣」第 300 题：「最长上升子序列」。我经常在这一步「摔跟头」，请各位也留意。

**参考代码 3**：根据「状态转移方程 1」

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        int[] dp = new int[len];
        dp[0] = nums[0];

        for (int i = 1; i < len; i++) {
            if (dp[i - 1] >= 0) {
                //因为dp[i]表示的是要以nums[i]结尾的连续数组的最大值，所以必须包含nums[i]
                //所以要判断的是要不要舍弃之前的dp
                dp[i] = dp[i - 1] + nums[i];
            } else {
                //如果前面的dp是负数，那负数加法只会成累赘，直接抛弃前面的负数，不用关心nums[i]
                //是正是负
                dp[i] = nums[i];
            }
        }

        // 最后不要忘记全部看一遍求最大值
        int res = dp[0];
        for (int i = 1; i < len; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }

}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

**参考代码 4**：根据「状态转移方程 2」

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        int[] dp = new int[len];
        dp[0] = nums[0];

        for (int i = 1; i < len; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
        }

        int res = dp[0];
        for (int i = 1; i < len; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

**复杂度分析**：

- 时间复杂度：O(N)；
- 空间复杂度：O(N)。

##### 第 5 步：思考优化空间

既然当前状态只与上一个状态有关，我们可以将空间复杂度降到 O(1)。

```java
public class Solution {

    public int maxSubArray(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        // 起名叫 pre 表示的意思是「上一个状态」的值
        int pre = nums[0];
        int res = pre;
        for (int i = 1; i < len; i++) {
            pre = Math.max(nums[i], pre + nums[i]);
            res = Math.max(res, pre);
        }
        return res;
    }

}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/dong-tai-gui-hua-fen-zhi-fa-python-dai-ma-java-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210520030657773](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520030658.png)

![image-20210520030749039](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520030749.png)

#### （3）分治法





![image-20210520035348889](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520035349.png)

![image-20210520035442244](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520035442.png)

![image-20210520040820290](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520040820.png)

###### 代码

```java
public int maxSubArray(int[] nums) {
    return maxSubArrayDivideWithBorder(nums, 0, nums.length-1);
}

private int maxSubArrayDivideWithBorder(int[] nums, int start, int end) {
    if (start == end) {
        // 只有一个元素，也就是递归的结束情况
        return nums[start];
    }

    // 计算中间值
    int center = (start + end) / 2;
    int leftMax = maxSubArrayDivideWithBorder(nums, start, center); // 计算左侧子序列最大值
    int rightMax = maxSubArrayDivideWithBorder(nums, center + 1, end); // 计算右侧子序列最大值

    // 下面计算横跨两个子序列的最大值
	//分治，所以最后一步肯定左右两边都是一个元素的，所以最大值都是自己了
    //然后到下面来，就是看中间往两边扩散的最大值了
    // 计算包含左侧子序列最后一个元素的子序列最大值
    int leftCrossMax = nums[center]; // 初始化一个值
    int leftCrossSum = 0;
    for (int i = center ; i >= start ; i --) {
        //这里是中间从右到左往左边扩散，不断的累加左边的元素的话，最大值，这个是中间序列的左半边的
        //最大值
        leftCrossSum += nums[i];
        leftCrossMax = Math.max(leftCrossSum, leftCrossMax);
    }

    // 计算包含右侧子序列最后一个元素的子序列最大值
    int rightCrossMax = nums[center+1];
    int rightCrossSum = 0;
    for (int i = center + 1; i <= end ; i ++) {
        rightCrossSum += nums[i];
        //这个是中间序列右半边的最大值，因为是连续的子序列，所以必须按顺序累加，也可能直接就是
        //累加开始的第一个元素，然后就是rightCrossMax
        rightCrossMax = Math.max(rightCrossSum, rightCrossMax);
    }

    // 计算跨中心的子序列的最大值
    int crossMax = leftCrossMax + rightCrossMax;

    // 比较三者，返回最大值
    return Math.max(crossMax, Math.max(leftMax, rightMax));
}

作者：lizhiqiang-3
链接：https://leetcode-cn.com/problems/maximum-subarray/solution/zheng-li-yi-xia-kan-de-dong-de-da-an-by-lizhiqiang/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析



## *10、[爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

![image-20210120022650652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120022651.png)

### 思路



这题我们可以参照之前分析的青蛙跳台阶问题，其实原理是完全一样的
我们来分析一下：

当n等于1的时候，只需要跳一次即可，只有一种跳法，记f(1)=1

当n等于2的时候，可以先跳一级再跳一级，或者直接跳二级，共有2种跳法，记f(2)=2

当n等于3的时候，他可以从一级台阶上跳两步上来，也可以从二级台阶上跳一步上来，所以总共有f(3)=f(2)+f(1)；

同理当等于n的时候，总共有f(n)=f(n-1)+f(n-2)(这里n>2)种跳法。

![image-20210120022808667](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120022808.png)

所以如果我们求上到n阶有多少种，代码很简单，直接递归就行

#### 1、递归

```java
    public static int climbStairs(int n) {
        if (n <= 1)
            return 1;
        if (n < 3)
            return n;
        return climbStairs(n - 1) + climbStairs(n - 2);
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/climbing-stairs/solution/javaqing-wa-tiao-tai-jie-he-fei-bo-na-qi-shu-lie-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

这种当n比较大的时候会超时，所以不推荐，但如果还想使用递归我们可以改为**尾递归**的方式，我们来看下代码

![image-20210120023321581](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120023321.png)

#### 2，非递归

但递归由于重复计算，导致效率很差，我们来看一下，有很多重复的计算，相同的颜色表示计算多次

![image-20210120023400196](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120023400.png)

![image-20210120023459217](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120023459.png)

#### 3、动态规划

我们看到上面的数组当前值是依赖他前面两个值的（前两个除外），我们只需要用两个临时变量即可，不需要申请一个数组，这个也是击败了100%的用户，即下个数都是前面两个数的第二个数和两个数和的加法结果

![image-20210520230528189](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520230529.png)

![fig1](https://assets.leetcode-cn.com/solution-static/70/70_fig1.gif)

```java
    public int climbStairs(int n) {
        if (n <= 2)
            return n;
        int first = 1, second = 2, sum = 0;
        //前两个已经求了，所以给定一个n，只要求n-2次
        while (n-- > 2) {
            sum = first + second;
            first = second;
            second = sum;
        }
        return sum;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/climbing-stairs/solution/javaqing-wa-tiao-tai-jie-he-fei-bo-na-qi-shu-lie-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    public int climbStairs(int n) {
        int p = 0, q = 0, r = 1;
        for (int i = 1; i <= n; ++i) {
            p = q; 
            q = r; 
            r = p + q;
        }
        return r;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/climbing-stairs/solution/pa-lou-ti-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210520230344798](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520230345.png)

![image-20210520231702267](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520231703.png)

## *11、[对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

![image-20210120030800775](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120030800.png)

### 思路

#### 递归实现

乍一看无从下手，但用递归其实很好解决。
根据题目的描述，镜像对称，就是左右两边相等，也就是左子树和右子树是相当的。
注意这句话，左子树和右子相等，也就是说要递归的比较左子树和右子树。
我们将根节点的左子树记做 left，右子树记做 right。比较 left 是否等于 right，不等的话直接返回就可以了。
如果相当，比较 left 的左节点和 right 的右节点，再比较 left 的右节点和 right 的左节点
比如看下面这两个子树(他们分别是根节点的左子树和右子树)，能观察到这么一个规律：
左子树 22 的左孩子 == 右子树 22 的右孩子
左子树 22 的右孩子 == 右子树 22 的左孩子

```json
    2         2
   / \       / \
  3   4     4   3
 / \ / \   / \ / \
8  7 6  5 5  6 7  8
```

根据上面信息可以总结出递归函数的两个条件：
终止条件：

left 和 right 不等，或者 left 和 right 都为空
递归的比较 left，left 和 right.right，递归比较 left，right 和 right.left
动态图如下：

![2.gif](https://pic.leetcode-cn.com/2449af8862537df2cbbc45a07764415c1a10769677c822fa271ea7447c8fa128-2.gif)

算法的时间复杂度是 O(n)，因为要遍历 nn 个节点
空间复杂度是 O(n)，空间复杂度是递归的深度，也就是跟树高度有关，最坏情况下树变成一个链表结构，高度是nn。
代码实现：

```java
class Solution {
	public boolean isSymmetric(TreeNode root) {
		if(root==null) {
			return true;
		}
		//调用递归函数，比较左节点，右节点
		return dfs(root.left,root.right);
	}
	
	boolean dfs(TreeNode left, TreeNode right) {
		//递归的终止条件是两个节点都为空
		//或者两个节点中有一个为空
		//或者两个节点的值不相等
		if(left==null && right==null) {
			return true;
		}
		if(left==null || right==null) {
			return false;
		}
		if(left.val!=right.val) {
			return false;
		}
		//再递归的比较 左节点的左孩子 和 右节点的右孩子
		//以及比较  左节点的右孩子 和 右节点的左孩子
		return dfs(left.left,right.right) && dfs(left.right,right.left);
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/symmetric-tree/solution/dong-hua-yan-shi-101-dui-cheng-er-cha-shu-by-user7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210520232455774](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520232456.png)

#### 队列实现

回想下递归的实现：
当两个子树的根节点相等时，就比较:
左子树的 left 和 右子树的 right，这个比较是用递归实现的。
现在我们改用队列来实现，思路如下：
首先从队列中拿出两个节点(left 和 right)比较
将 left 的 left 节点和 right 的 right 节点放入队列
将 left 的 right 节点和 right 的 left 节点放入队列
时间复杂度是 O(n)，空间复杂度是 O(n)
动画演示如下：

![1.gif](https://pic.leetcode-cn.com/45a663b08efaa14193d63ef63ae3d1d130807467d13707f584906ad3af4adc36-1.gif)

![image-20210120031240030](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210120031240.png)

**每次出了一对的结点，后面就把这对结点的子节点全部入队**

```java
class Solution {
	public boolean isSymmetric(TreeNode root) {
		if(root==null || (root.left==null && root.right==null)) {
			return true;
		}
		//用队列保存节点
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		//将根节点的左右孩子放到队列中
		queue.add(root.left);
		queue.add(root.right);
		while(queue.size()>0) {
			//从队列中取出两个节点，再比较这两个节点
			TreeNode left = queue.removeFirst();
			TreeNode right = queue.removeFirst();
			//如果两个节点都为空就继续循环，两者有一个为空就返回false
			if(left==null && right==null) {
				continue;
			}
			if(left==null || right==null) {
				return false;
			}
			if(left.val!=right.val) {
				return false;
			}
			//将左节点的左孩子， 右节点的右孩子放入队列
			queue.add(left.left);
			queue.add(right.right);
			//将左节点的右孩子，右节点的左孩子放入队列
			queue.add(left.right);
			queue.add(right.left);
		}
		
		return true;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/symmetric-tree/solution/dong-hua-yan-shi-101-dui-cheng-er-cha-shu-by-user7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210520233329881](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520233330.png)

## *12、[二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

![image-20210123223111142](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210123223112.png)

### 思路

#### 递归

```java
    /**
     * 递归实现二叉树最大深度
     * 时间复杂度O(n)
     * 空间复杂度:线性表最差O(n)、二叉树完全平衡最好O(logn)
     *
     * @param root 根节点
     * @return 最大深度
     */
    private static int maxDepth(TreeNode root) {
        //递归退出条件，到叶子节点
        if (root == null) {
            return 0;
        }
        //计算左子树最大深度
        int leftMaxDepth = maxDepth(root.left);
        //计算右子树最大深度
        int rightMaxDepth = maxDepth(root.right);
        //以某个节点为根节点的数的最大深度为max
        //max=max(leftMaxDepth,rightMaxDepth)+1
        return Math.max(leftMaxDepth, rightMaxDepth) + 1;
    }


作者：zhengzhong
链接：https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/solution/javashi-xian-san-chong-fang-fa-di-gui-shi-xian-die/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210123225749426](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210123225749.png)

###### 复杂度分析

![image-20210520234013192](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210520234014.png)



#### BFS 广度优先算法

BFS的实现原理就是一层层遍历，统计一下总共有多少层，我们来画个图分析一下。

![image.png](https://pic.leetcode-cn.com/4d908dab4fe456418f3c06a124c4a0391c67f19780bfafc24d33878541faa665-image.png)

```java
    /**
     * BFS迭代实现二叉树最大深度
     * 时间复杂度O(n)
     * 空间复杂度:线性表最差O(n)、二叉树完全平衡最好O(logn)
     *
     * @param root 根节点
     * @return 最大深度
     */
    private static int maxDepth1(TreeNode root) {
        if (root == null) {
            return 0;
        }
        //BFS的层次遍历思想，记录二叉树的层数，
        //遍历完，层数即为最大深度
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int maxDepth = 0;
        while (!queue.isEmpty()) {
            maxDepth++;
            int levelSize = queue.size();
            for (int i = 0; i < levelSize; i++) {
                TreeNode node = queue.pollFirst();
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
        }
        return maxDepth;
    }

作者：zhengzhong
链接：https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/solution/javashi-xian-san-chong-fang-fa-di-gui-shi-xian-die/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### DFS前序遍历思想

```java
    /**
     * DFS迭代实现二叉树最大深度
     * 时间复杂度O(n)
     * 空间复杂度:线性表最差O(n)、二叉树完全平衡最好O(logn)
     *
     * @param root 根节点
     * @return 最大深度
     */
    private static int maxDepth2(TreeNode root) {
        if (root == null) {
            return 0;
        }
        LinkedList<Pair<TreeNode, Integer>> stack = new LinkedList<>();
        stack.push(new Pair<>(root, 1));
        int maxDepth = 0;
        //DFS实现前序遍历，每个节点记录其所在深度
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode node = pair.first;
            //DFS过程不断比较更新最大深度
            maxDepth = Math.max(maxDepth, pair.second);
            //记录当前节点所在深度
            int curDepth = pair.second;
            //当前节点的子节点入栈，同时深度+1
            if (node.right != null) {
                stack.push(new Pair<>(node.right, curDepth + 1));
            }
            if (node.left != null) {
                stack.push(new Pair<>(node.left, curDepth + 1));
            }
        }
        return maxDepth;
    }


作者：zhengzhong
链接：https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/solution/javashi-xian-san-chong-fang-fa-di-gui-shi-xian-die/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        int ans = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size > 0) {
                TreeNode node = queue.poll();
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
                size--;
            }
            ans++;
        }
        return ans;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/solution/er-cha-shu-de-zui-da-shen-du-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210123231207340](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210123231207.png)

###### 复杂度分析

![image-20210521010905505](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20210521010905505.png)



![image-20210521011017216](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521011018.png)

###### DFS与BFS不同

DFS深度遍历，每次只是入栈当前节点的子节点，

BFS广度优先，每次都入栈当前层的所有节点的子节点

## *13、[买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

![image-20210123235635490](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210123235635.png)

### 思路

#### （1）贪心算法

贪心算法，从左向右，维护一个最小值low，与每一天的股票价格做差，差最大的为答案

![image-20210523172416497](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172417.png)

![image-20210523172432606](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172433.png)

```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxP = 0;
        int low = Integer.MAX_VALUE;
        for(int p : prices) {
            if(p < low) {
                //因为low的下标要《=当前的，所以不能先for循环得到low最低价，而是计算当前的
                //p的时候前面的最低价，算差价
                low = p;
            }
            maxP = Math.max(maxP, p - low);
        }
        return maxP;
    }
}

作者：pendygg
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/hen-jing-dian-de-tan-xin-suan-fa-by-pendygg/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210523172706719](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172707.png)

![image-20210523172840809](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172841.png)



#### （2）动态规划

题目只问最大利润，没有问这几天具体哪一天买、哪一天卖，因此可以考虑使用 动态规划 的方法来解决。

买卖股票有约束，根据题目意思，有以下两个约束条件：

条件 1：你不能在买入股票前卖出股票；
条件 2：最多只允许完成一笔交易。

因此 **当天是否持股** 是一个很重要的因素，而当前是否持股和昨天是否持股有关系，为此我们需要把 **是否持股** 设计到状态数组中。

![image-20210124152518306](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210124152551.png)

```java
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        // 特殊判断
        if (len < 2) {
            return 0;
        }
        int[][] dp = new int[len][2];

        // dp[i][0] 下标为 i 这天结束的时候，不持股，手上拥有的现金数
        // dp[i][1] 下标为 i 这天结束的时候，持股，手上拥有的现金数

        // 初始化：不持股显然为 0，持股就需要减去第 1 天（下标为 0）的股价
        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        // 从第 2 天开始遍历
        for (int i = 1; i < len; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            //因为只允许交易一次，所以如果是昨天没买，那手头现金一直是0，今天买入就直接变负了
            //可以理解为赊账买股票赚差价
            dp[i][1] = Math.max(dp[i - 1][1], -prices[i]);
        }
        return dp[len - 1][0];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/bao-li-mei-ju-dong-tai-gui-hua-chai-fen-si-xiang-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210523171309147](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523171409.png)

![image-20210523172040934](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172042.png)

**参考代码 4**：（空间优化）

说明：空间优化只看状态转移方程。

状态转移方程里下标为 i 的行只参考下标为 i - 1 的行（即只参考上一行），并且：

下标为 i 的行并且状态为 0 的行参考了上一行状态为 0 和 1 的行；
下标为 i 的行并且状态为 1 的行只参考了上一行状态为 1 的行。

```java
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len < 2) {
            return 0;
        }

        int[] dp = new int[2];
        dp[0] = 0;
        dp[1] = -prices[0];
        for (int i = 1; i < len; i++) {
            //可以从上次循环得到的昨天的手头现金数dp[0]和dp[1]加上今天的股价，推算出今天的
            dp[0] = Math.max(dp[0], dp[1] + prices[i]);
            dp[1] = Math.max(dp[1], -prices[i]);
        }
        return dp[0];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/bao-li-mei-ju-dong-tai-gui-hua-chai-fen-si-xiang-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

**复杂度分析**：

- 时间复杂度：O(N)，遍历股价数组可以得到最优解；
- 空间复杂度：O(1)，状态数组的长度为 22。

![image-20210523171857153](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523171857.png)

## *14、[只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

![image-20210523172937387](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523172938.png)

### 思路

#### （1）异或

![image-20210124163012148](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210124163012.png)



```java
class Solution {
    public int singleNumber(int[] nums) {
        int single = 0;
        for (int num : nums) {
            single ^= num;
        }
        return single;
    }
}

作者：demigodliu
链接：https://leetcode-cn.com/problems/single-number/solution/wu-tu-guan-fang-tui-jian-ti-jie-zhi-chu-2ttk9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210523173302591](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523173303.png)

## *15、[环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

![image-20210124172419446](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210124172419.png)

### 思路

#### 1，快慢指针解决

判断链表是否有环应该是老生常谈的一个话题了，最简单的一种方式就是快慢指针，慢指针针每次走一步，快指针每次走两步，如果相遇就说明有环，如果有一个为空说明没有环。代码比较简单

```java
public boolean hasCycle(ListNode head) {
    if (head == null)
        return false;
    //快慢两个指针
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        //慢指针每次走一步
        slow = slow.next;
        //快指针每次走两步
        fast = fast.next.next;
        //如果相遇，说明有环，直接返回true
        if (slow == fast)
            return true;
    }
    //否则就是没环
    return false;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/linked-list-cycle/solution/3chong-jie-jue-fang-shi-liang-chong-ji-bai-liao-10/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210124164557139](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210124164557.png)



#### 2，存放到集合中

这题还可以把节点存放到集合set中，每次存放的时候判断当前节点是否存在，如果存在，说明有环，直接返回true，比较容易理解

```java
public boolean hasCycle(ListNode head) {
    Set<ListNode> set = new HashSet<>();
    while (head != null) {
        //如果重复出现说明有环
        if (set.contains(head))
            return true;
        //否则就把当前节点加入到集合中
        set.add(head);
        head = head.next;
    }
    return false;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/linked-list-cycle/solution/3chong-jie-jue-fang-shi-liang-chong-ji-bai-liao-10/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210523174103866](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523174104.png)

#### 3、反转链表



关于链表的反转可以看下[432，剑指 Offer-反转链表的3种方式](https://mp.weixin.qq.com/s?__biz=MzU0ODMyNDk0Mw==&mid=2247488340&idx=1&sn=c3d6adc9f737672aab544931502dda2e&chksm=fb418074cc360962b46cb764068a5818f58bed6a4cd05ef61057823918d95f3a192550f02408&token=723562173&lang=zh_CN#rd)。`如果有环，那么链表反转之后，原来的头结点和反转之后的头结点一定是同一个`

```java
    public ListNode reverseList(ListNode head) {
        //新链表
        ListNode newHead = null;
        //虽然把head丢进来了，但是最后并没有返回head的引用，所以调用方法里的head还是指向最开始的
        //头结点的
        while (head != null) {
            //先保存访问的节点的下一个节点，保存起来
            //留着下一步访问的，如果此时的head遍历到图中的2，就是循环点，2的next已经被改成3
            //了，而不是0，就是原来的链表顺序已经被打乱反转了，所以最后又会 把整个链表挂到3后面
            //3后面是null，所以中断循环了
            ListNode temp = head.next;
            //每次访问的原链表节点都会成为新链表的头结点，
            //其实就是把新链表挂到访问的原链表节点的
            //后面就行了
            head.next = newHead;
            //更新新链表
            newHead = head;
            //重新赋值，继续访问
            head = temp;
        }
        //返回新链表
        return newHead;
    }

    public boolean hasCycle(ListNode head) {
        //其实这里的引用也是复制一份的，所以在被调方法里，head怎么移动，只要还是使用原来的head
        //引用，head还是指向最开始的头结点的
        ListNode rev = reverseList(head);
        if (head != null && head.next != null && rev == head) {
            return true;
        }
        return false;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/linked-list-cycle/solution/3chong-jie-jue-fang-shi-liang-chong-ji-bai-liao-10/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210124172028412](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210124181156.png)

## *16、[相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

![image-20210130224736943](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210130224737.png)

### 思路

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210130225412.png)

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode pA = headA, pB = headB;
        while (pA != pB) {
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}

作者：kkbill
链接：https://leetcode-cn.com/problems/intersection-of-two-linked-lists/solution/gei-zi-ji-liu-ge-bi-ji-by-kkbill/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## *17、[多数元素](https://leetcode-cn.com/problems/majority-element/)



### ![image-20210525054907574](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525054908.png)思路：

#### 1、排序思路

![image-20210130233005826](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210130233005.png)

###### 复杂度分析

![image-20210525055224399](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525055225.png)

![image-20210525055750084](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525055751.png)

#### 2、摩尔投票法思路

![摩尔投票法.gif](https://pic.leetcode-cn.com/02996aa0ee5ca61b3bdf5799b8fbdd559c9eb7f90ab3a7f21fab3c4c6e0a9e7a-file_1564132293385)

![image-20210130233234153](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210130233234.png)

```java
class Solution {
    public int majorityElement(int[] nums) {
        int cand_num = nums[0], count = 1;
        //如果当前数是候选数，就+1票，不是就减1票，如果count=0说明候选数不是众数，换成当前数
        //最后的候选数的count一定>0
        for (int i = 1; i < nums.length; ++i) {
            if (cand_num == nums[i])
                ++count;
            else if (--count == 0) {
                cand_num = nums[i];
                count = 1;
            }
        }
        return cand_num;
    }
}

作者：gfu
链接：https://leetcode-cn.com/problems/majority-element/solution/3chong-fang-fa-by-gfu-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210525055649498](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525055650.png)



## 18、[反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)



![image-20210131032610864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210131032610.png)

### 思路

#### 1、双指针迭代



![image-20210131032601200](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210131032601.png)

![image-20210131032717091](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210131032717.png)

#### 2、递归

![image-20210131034502430](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210131034502.png)

![递归.gif](https://pic.leetcode-cn.com/dacd1bf55dec5c8b38d0904f26e472e2024fc8bee4ea46e3aa676f340ba1eb9d-%E9%80%92%E5%BD%92.gif)

以1->2->3->4->5这个链表为例，整个递归调用的执行过程，对应到代码层面(用java做示范)是怎么执行的，以及递归的调用栈都列出来了，请点击下面的幻灯片查看吧。

![img](https://pic.leetcode-cn.com/c573939fa872edd4da5ac39e703bccdc65c32849a95ba7637dea5cfdfb6eced6-%E5%B9%BB%E7%81%AF%E7%89%871.jpg)

![img](https://pic.leetcode-cn.com/bfa449ed16ecfc905f8ef8e9b049ed397b608f30dad84d37b2005e19797b1d49-%E5%B9%BB%E7%81%AF%E7%89%872.jpg)

![img](https://pic.leetcode-cn.com/c790acd5bd2ebf285ef2f687d76c9a48f2e32418acdb32909cc76cc069501ac7-%E5%B9%BB%E7%81%AF%E7%89%873.jpg)

![img](https://pic.leetcode-cn.com/d078fc5c224964b468210b205838ecac26efde1a0bd6fade6f3a758a87b82068-%E5%B9%BB%E7%81%AF%E7%89%874.jpg)

![img](https://pic.leetcode-cn.com/cd9c77c8c7873c4aa39bbb7a185b729151516021cee7009575c258fb82f77383-%E5%B9%BB%E7%81%AF%E7%89%875.jpg)

![img](https://pic.leetcode-cn.com/f89c9e095d414b8366dd7d490508ba9c99d6c93953b4429168af262169f18e83-%E5%B9%BB%E7%81%AF%E7%89%876.jpg)

![img](https://pic.leetcode-cn.com/485dbe9ad44ab7b05e01c46a1bc1718187a01f8a9fe8331f497e7011f9508b57-%E5%B9%BB%E7%81%AF%E7%89%877.jpg)

![img](https://pic.leetcode-cn.com/04550182527e6570d9d04f2eeae330848b83d3ff13b23ea4153410210586dc85-%E5%B9%BB%E7%81%AF%E7%89%878.jpg)

![img](https://pic.leetcode-cn.com/b167f5ee08eafab82d680cf782898b649dd2a4453cd122ad690e83e35c387a11-%E5%B9%BB%E7%81%AF%E7%89%879.jpg)

![img](https://pic.leetcode-cn.com/950f6edfb553cbeec65ce3c9679dd8d3401fd1837ad9eb16989217bf83f30e58-%E5%B9%BB%E7%81%AF%E7%89%8710.jpg)

![img](https://pic.leetcode-cn.com/dcd5bef9b8ff3de98e9533da7da3a8411643bbef9bc5e1a576085b4403197649-%E5%B9%BB%E7%81%AF%E7%89%8711.jpg)

![img](https://pic.leetcode-cn.com/1d6aaab3d9a42c20420fb6087e520ea05bd6d0789213f228481e55891b847b1e-%E5%B9%BB%E7%81%AF%E7%89%8712.jpg)

![img](https://pic.leetcode-cn.com/74bb9de62d3bb36a9c174d7702c7116974838b46eb96933e2921c054a34fa5d9-%E5%B9%BB%E7%81%AF%E7%89%8713.jpg)

![img](https://pic.leetcode-cn.com/e2bea51573eb5a763b5b9c0873887ced8932db95bc6bf4ff5befb363c8960984-%E5%B9%BB%E7%81%AF%E7%89%8714.jpg)

![img](https://pic.leetcode-cn.com/95db06ce86448f3265b0f3178baf7372915601e4b367fcde54ff5cf509532bc8-%E5%B9%BB%E7%81%AF%E7%89%8715.jpg)

![img](https://pic.leetcode-cn.com/fc96f0786c05a3a2ae0987757b4f569b3b18e59da0b5cb3afb063b9fc0ace069-%E5%B9%BB%E7%81%AF%E7%89%8716.jpg)

```java
class Solution {
	public ListNode reverseList(ListNode head) {
		//递归终止条件是当前为空，或者下一个节点为空
		if(head==null || head.next==null) {
			return head;
		}
		//这里的cur就是最后一个节点
		ListNode cur = reverseList(head.next);
		//这里请配合动画演示理解
		//如果链表是 1->2->3->4->5，那么此时的cur就是5
		//而head是4，head的下一个是5，下下一个是空
		//所以head.next.next 就是5->4
		head.next.next = head;
		//防止链表循环，需要将head.next设置为空
		head.next = null;
		//每层递归函数都返回cur，也就是最后一个节点
		return cur;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/reverse-linked-list/solution/dong-hua-yan-shi-206-fan-zhuan-lian-biao-by-user74/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 19、[翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

![image-20210201010423685](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210201010424.png)

### 思路

#### 1、递归

我们在做二叉树题目时候，第一想到的应该是用 递归 来解决。
仔细看下题目的 输入 和 输出，输出的左右子树的位置跟输入正好是相反的，于是我们可以递归的交换左右子树来完成这道题。
看一下动画就明白了：

![226_2.gif](https://pic.leetcode-cn.com/0f91f7cbf5740de86e881eb7427c6c3993f4eca3624ca275d71e21c5e3e2c550-226_2.gif)

其实就是交换一下左右节点，然后再递归的交换左节点，右节点
根据动画图我们可以总结出递归的两个条件如下：

终止条件：当前节点为 null 时返回
交换当前节点的左右节点，再递归的交换当前节点的左节点，递归的交换当前节点的右节点

**交换了两个节点，这两个节点的子节点也就跟着换了过来，再交换子节点就好了**

```java
class Solution {
	public TreeNode invertTree(TreeNode root) {
		//递归函数的终止条件，节点为空时返回
		if(root==null) {
			return null;
		}
		//下面三句是将当前节点的左右子树交换
		TreeNode tmp = root.right;
		root.right = root.left;
		root.left = tmp;
		//递归交换当前节点的 左子树
		invertTree(root.left);
		//递归交换当前节点的 右子树
		invertTree(root.right);
		//函数返回时就表示当前这个节点，以及它的左右子树
		//都已经交换完了
		return root;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/invert-binary-tree/solution/dong-hua-yan-shi-liang-chong-shi-xian-226-fan-zhua/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 2、迭代

递归实现也就是深度优先遍历的方式，那么对应的就是**广度优先遍历**。
广度优先遍历需要额外的数据结构--**队列**，来存放临时遍历到的元素。
深度优先遍历的特点是一竿子插到底，不行了再退回来继续；而广度优先遍历的特点是**层层扫荡**。
所以，我们需要先将根节点放入到队列中，然后不断的迭代队列中的元素。
对当前元素调换其左右子树的位置，然后：

判断其左子树是否为空，不为空就放入队列中
判断其右子树是否为空，不为空就放入队列中
动态图如下：

![226_迭代.gif](https://pic.leetcode-cn.com/f9e06159617cbf8372b544daee37be70286c3d9b762c016664e225044fc4d479-226_%E8%BF%AD%E4%BB%A3.gif)

深度优先遍历和广度优先遍历，从动画图中看起来很类似，这是因为演示的树层数只有三层。
时间复杂度：同样每个节点都需要入队列/出队列一次，所以是 O(n)O(n)
空间复杂度：最坏的情况下会包含所有的叶子节点，完全二叉树叶子节点是 n/2个，所以时间复杂度是 0(n)0(n)

```java
class Solution {
	public TreeNode invertTree(TreeNode root) {
		if(root==null) {
			return null;
		}
		//将二叉树中的节点逐层放入队列中，再迭代处理队列中的元素
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		queue.add(root);
		while(!queue.isEmpty()) {
			//每次都从队列中拿一个节点，并交换这个节点的左右子树
			TreeNode tmp = queue.poll();
			TreeNode left = tmp.left;
			tmp.left = tmp.right;
			tmp.right = left;
			//如果当前节点的左子树不为空，则放入队列等待后续处理
			if(tmp.left!=null) {
				queue.add(tmp.left);
			}
			//如果当前节点的右子树不为空，则放入队列等待后续处理
			if(tmp.right!=null) {
				queue.add(tmp.right);
			}
			
		}
		//返回处理完的根节点
		return root;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/invert-binary-tree/solution/dong-hua-yan-shi-liang-chong-shi-xian-226-fan-zhua/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210201011918563](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210201011918.png)

## 20、[回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

![image-20210201014855584](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210201014855.png)

### 思路

#### 1、快慢指针反转链表

这题是让判断链表是否是回文链表，所谓的回文链表就是以链表中间为中心点两边对称。我们常见的有判断一个字符串是否是回文字符串，这个比较简单，可以使用两个指针，一个最左边一个最右边，两个指针同时往中间靠，判断所指的字符是否相等。

但这题判断的是链表，因为这里是单向链表，只能从前往后访问，不能从后往前访问，所以使用判断字符串的那种方式是行不通的。但我们可以通过找到链表的中间节点然后把链表后半部分反转（

[关于链表的反转可以看下432，剑指]: https://mp.weixin.qq.com/s?__biz=MzU0ODMyNDk0Mw==&amp;mid=2247488340&amp;idx=1&amp;sn=c3d6adc9f737672aab544931502dda2e&amp;chksm=fb418074cc360962b46cb764068a5818f58bed6a4cd05ef61057823918d95f3a192550f02408&amp;scene=21#wechat_redirect

），最后再用后半部分反转的链表和前半部分一个个比较即可。这里以示例2为例画个图看一下。

![image.png](https://pic.leetcode-cn.com/1603414119-KfHINu-image.png)



![image.png](https://pic.leetcode-cn.com/1603414128-PsmRse-image.png)

```java
public boolean isPalindrome(ListNode head) {
    ListNode fast = head, slow = head;
    //通过快慢指针找到中点
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
    }
    //如果fast不为空，说明链表的长度是奇数个
    if (fast != null) {
        slow = slow.next;
    }
    //反转后半部分链表
    slow = reverse(slow);

    fast = head;
    while (slow != null) {
        //然后比较，判断节点值是否相等
        if (fast.val != slow.val)
            return false;
        fast = fast.next;
        slow = slow.next;
    }
    return true;
}

//反转链表
public ListNode reverse(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = prev;
        prev = head;
        head = next;
    }
    return prev;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/palindrome-linked-list/solution/di-gui-zhan-deng-3chong-jie-jue-fang-shi-zui-hao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 快慢指针的原理

快指针到达末尾后，走过的路径肯定是慢指针的一倍，所以慢指针就在中间了

![image-20210201023633360](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210201023633.png)

#### 2，使用栈解决

我们知道栈是先进后出的一种数据结构，这里还可以使用栈先把链表的节点全部存放到栈中，然后再一个个出栈，这样就相当于链表从后往前访问了，通过这种方式也能解决，看下代码

```java
public boolean isPalindrome(ListNode head) {
    ListNode temp = head;
    Stack<Integer> stack = new Stack();
    //把链表节点的值存放到栈中
    while (temp != null) {
        stack.push(temp.val);
        temp = temp.next;
    }

    //然后再出栈
    while (head != null) {
        if (head.val != stack.pop()) {
            return false;
        }
        head = head.next;
    }
    return true;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/palindrome-linked-list/solution/di-gui-zhan-deng-3chong-jie-jue-fang-shi-zui-hao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

这里相当于链表从前往后全部都比较了一遍，其实我们只需要拿链表的后半部分和前半部分比较即可，没必要全部比较，所以这里可以优化一下

```java
public boolean isPalindrome(ListNode head) {
    if (head == null)
        return true;
    ListNode temp = head;
    Stack<Integer> stack = new Stack();
    //链表的长度
    int len = 0;
    //把链表节点的值存放到栈中
    while (temp != null) {
        stack.push(temp.val);
        temp = temp.next;
        len++;
    }
    //len长度除以2
    len >>= 1;
    //然后再出栈
    while (len-- >= 0) {
        if (head.val != stack.pop())
            return false;
        head = head.next;
    }
    return true;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/palindrome-linked-list/solution/di-gui-zhan-deng-3chong-jie-jue-fang-shi-zui-hao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

看一下运行结果，这种效率就差了很多

![image.png](https://pic.leetcode-cn.com/1603414335-XdQoJd-image.png)

#### 3，递归方式解决

我们知道，如果对链表逆序打印可以这样写

```java
private void printListNode(ListNode head) {
    if (head == null)
        return;
    printListNode(head.next);
    System.out.println(head.val);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/palindrome-linked-list/solution/di-gui-zhan-deng-3chong-jie-jue-fang-shi-zui-hao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

也就是说最先打印的是链表的尾结点，他是从后往前打印的，如果以后谁再给你说单向链表不能从后往前遍历，你就拿这段代码怼他，哈哈。看到这里是不是有灵感了，我们来对上面的对面进行改造一下

```java
ListNode temp;

public boolean isPalindrome(ListNode head) {
    temp = head;
    return check(head);
}

private boolean check(ListNode head) {
    if (head == null)
        return true;
    boolean res = check(head.next) && (temp.val == head.val);
    temp = temp.next;
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/palindrome-linked-list/solution/di-gui-zhan-deng-3chong-jie-jue-fang-shi-zui-hao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image.png](https://pic.leetcode-cn.com/1603414442-flcTKw-image.png)

## 21、[ 移动零](https://leetcode-cn.com/problems/move-zeroes/)

![image-20210203025422009](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210203025422.png)

### 思路

#### 1、两次遍历

- 遍历数组，无为0的元素移动数组前方，用index下标记录。
- 遍历结束，对index值后的元素统一设为0

```java
class Solution {
    public void moveZeroes(int[] nums) {
      int index = 0;
      for(int num:nums){
          if(num!=0){
              nums[index++]=num;
          }
      }
      while(index<nums.length){
          nums[index++] = 0;
      }
    }
}

作者：zhulg
链接：https://leetcode-cn.com/problems/move-zeroes/solution/0-ms-zai-suo-you-java-ti-jiao-zhong-ji-bai-liao--2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![283_1.gif](https://pic.leetcode-cn.com/9669b4ffb158eaeeee6f0cd66a70f24411575edab1ab8a037c4c9084b1c743f5-283_1.gif)

我们创建两个指针i和j，第一次遍历的时候指针j用来记录当前有多少非0元素。即遍历的时候每遇到一个非0元素就将其往数组左边挪，第一次遍历完后，j指针的下标就指向了最后一个非0元素下标。
第二次遍历的时候，起始位置就从j开始到结束，将剩下的这段区域内的元素全部置为0。
动画演示

```java
class Solution {
	public void moveZeroes(int[] nums) {
		if(nums==null) {
			return;
		}
		//第一次遍历的时候，j指针记录非0的个数，只要是非0的统统都赋给nums[j]
		int j = 0;
		for(int i=0;i<nums.length;++i) {
			if(nums[i]!=0) {
				nums[j++] = nums[i];
			}
		}
		//非0元素统计完了，剩下的都是0了
		//所以第二次遍历把末尾的元素都赋为0即可
		for(int i=j;i<nums.length;++i) {
			nums[i] = 0;
		}
	}
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/move-zeroes/solution/dong-hua-yan-shi-283yi-dong-ling-by-wang_ni_ma/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 2、一次遍历

这里参考了快速排序的思想，快速排序首先要确定一个待分割的元素做中间点x，然后把所有小于等于x的元素放到x的左边，大于x的元素放到其右边。
这里我们可以用0当做这个中间点，把不等于0(注意题目没说不能有负数)的放到中间点的左边，等于0的放到其右边。
这的中间点就是0本身，所以实现起来比快速排序简单很多，我们使用两个指针i和j，只要nums[i]!=0，我们就交换nums[i]和nums[j]
请对照动态图来理解：

![283_2.gif](https://pic.leetcode-cn.com/36d1ac5d689101cbf9947465e94753c626eab7fcb736ae2175f5d87ebc85fdf0-283_2.gif)

时间复杂度: O(n)
空间复杂度: O(1)

```java
class Solution {
	public void moveZeroes(int[] nums) {
		if(nums==null) {
			return;
		}
		//两个指针i和j
		int j = 0;
		for(int i=0;i<nums.length;i++) {
			//当前元素!=0，就把其交换到左边，等于0的交换到右边
			if(nums[i]!=0) {
				int tmp = nums[i];
				nums[i] = nums[j];
				nums[j++] = tmp;
			}
		}
	}
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/move-zeroes/solution/dong-hua-yan-shi-283yi-dong-ling-by-wang_ni_ma/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210203031119136](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210203031119.png)

**思路是把非0的全部往左边放**

b碰到一次0后，后面都只会指向0

## 22、[找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)

![image-20210203063818449](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210203063818.png)

### 思路

> 分析
>
> 根据题目特点，可以把数组中的元素与索引建立一一对应的关系。因为索引是确定的0到n-1,一个也不缺，而数组的元素不确定，少了哪个也不知道。
> 既然两者是一一对应的关系，那么我们对数组中的每个元素对应的索引做个标记；
> 然后再对索引进行一次遍历，那么不存的元素就不会对它对应的索引进行比较，由此可查找出这些不存在的元素。
> 思路
>
> 遍历每个元素，对索引进行标记
> 将对应索引位置的值变为负数；
> 遍历下索引，看看哪些索引位置上的数不是负数的。
> 位置上不是负数的索引，对应的元素就是不存在的。

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        //用来存放结果
        List<Integer> res = new ArrayList<>(); 
        //1. 遍历下数组的元素，对对应的索引位置的元素作标记
        int len = nums.length;
        for(int i = 0; i < len; i++){
            int num = Math.abs(nums[i]);  //由于数组的元素有可能被*-1，所以取绝对值
            int index = num - 1;
            if(nums[index] > 0){
                nums[index] *= -1;
            }
        }      
        // 寻找没有标记的索引位置
        for(int i = 0; i < len; i++){
            if(nums[i] > 0){
                int num = i + 1;  //将索引转化为对应的元素
                res.add(num);
            }     
        }
        return res;
    }
}

作者：xmblgt
链接：https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/solution/bu-xu-yao-e-wai-kong-jian-si-lu-chao-ji-qing-xi-bu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210203064724107](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210203064724.png)

![image-20210203070340214](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210203070340.png)

## 23、[汉明距离](https://leetcode-cn.com/problems/hamming-distance/)

![image-20210204022513247](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210204022513.png)

### 思路

#### 异或

> 先将两个数异或运算得到n，那么n里面1的个数就是结果，如果n不为0，那么n至少有一位是1。如果n减1，那么原来处在整数最右边的1就会变为0，原来在1后面的所有的0都会变成1。其余所有位将不会受到影响。这样右边这部分的&运算结果就为0，然后循环

```java
        int n=x^y;
        int count=0;
        while(n!=0){
        	count++;
        	n=n&(n-1);
        }
        return count;

作者：Charcl
链接：https://leetcode-cn.com/problems/hamming-distance/solution/xian-jiang-liang-ge-shu-yi-huo-yun-suan-ran-hou-za/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210204023156504](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210204023156.png)



## 24、[二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

![image-20210205012004273](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210205012005.png)

### 思路

首先我们知道一条路径的长度为**该路径经过的节点数减一**，所以求直径（即求路径长度的最大值）等效于求路径经过节点数的最大值减一。

而任意一条路径均可以被看作由某个节点为起点，从其左儿子和右儿子向下遍历的路径拼接得到。



![image-20210205014837002](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210205014837.png)





```java
class Solution {
    int ans;
    public int diameterOfBinaryTree(TreeNode root) {
        ans = 1;
        depth(root);
        return ans - 1;
    }
    public int depth(TreeNode node) {
        if (node == null) {
            return 0; // 访问到空节点了，返回0
        }
        int L = depth(node.left); // 左儿子为根的子树的深度
        int R = depth(node.right); // 右儿子为根的子树的深度
        ans = Math.max(ans, L+R+1); // 计算d_node即L+R+1 并更新ans
        return Math.max(L, R) + 1; // 返回该节点为根的子树的深度
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/diameter-of-binary-tree/solution/er-cha-shu-de-zhi-jing-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### **复杂度分析**

时间复杂度：O(N)O(N)，其中 NN 为二叉树的节点数，即遍历一棵二叉树的时间复杂度，每个结点只被访问一次。

空间复杂度：O(Height)O(Height)，其中 HeightHeight 为二叉树的高度。由于递归函数在递归过程中需要为每一层递归函数分配栈空间，所以这里需要额外的空间且该空间取决于递归的深度，而递归的深度显然为二叉树的高度，并且每次递归调用的函数里又只用了常数个变量，所以所需空间复杂度为 O(Height)O(Height) 。



L、R的数量认为是**节点的数量**就好理解了。路径的长度本来就是统计最多的结点数

**为什么最后返回的时候ans又要减1呢**

**深度等于节点数减一**

**ans首先指的是节点个数**，加的1上加了根节点

![img](https://pic.leetcode-cn.com/ea085785fdf66af2fc324c7e569deeff131520be15d01adce213dadf0d9cdd69-%E5%B9%BB%E7%81%AF%E7%89%871.JPG)

![img](https://pic.leetcode-cn.com/6b4ad3a450f429c06d4b7635b22710a8713c8e855cb799bed927f26f16bae0fc-%E5%B9%BB%E7%81%AF%E7%89%872.JPG)

![img](https://pic.leetcode-cn.com/14a924d419421b7ed6afba49d74cb0e4fe59a6ed4106c54fc5ddc0d3e048be2f-%E5%B9%BB%E7%81%AF%E7%89%873.JPG)

![img](https://pic.leetcode-cn.com/1866997d80a0ef0d6a8a59c1777d1d86056970c1f2a9effd0e70edfff236f764-%E5%B9%BB%E7%81%AF%E7%89%874.JPG)

![img](https://pic.leetcode-cn.com/fffc7160ad99a654d56d06fb426be2a0ef9d61c44e276bb01712bf7ed797ff21-%E5%B9%BB%E7%81%AF%E7%89%875.JPG)

![img](https://pic.leetcode-cn.com/ab168d4183d6cdf8c18877fb0749b14db184a35ac41082fab677335f121d7272-%E5%B9%BB%E7%81%AF%E7%89%876.JPG)

![img](https://pic.leetcode-cn.com/540d3dc942e4b9bfd04d589669e307055999ca442d90ec4ce37cfae851808e50-%E5%B9%BB%E7%81%AF%E7%89%877.JPG)

![img](https://pic.leetcode-cn.com/b277f28a894813160f22d958e84acdd50fbf46f8d34841bfe5013eaa30bbc1fc-%E5%B9%BB%E7%81%AF%E7%89%878.JPG)

![img](https://pic.leetcode-cn.com/680957d3fc0f97b8df1e9702294c5b9b3472493f99f4a0238b6855dc56f4ff4f-%E5%B9%BB%E7%81%AF%E7%89%879.JPG)

![img](https://pic.leetcode-cn.com/bdb2395f9d30edcc515fabed62ab24a89b3d6c7c75237cc7fea27bb96852f5d4-%E5%B9%BB%E7%81%AF%E7%89%8710.JPG)

![img](https://pic.leetcode-cn.com/5a89090d8246647d64c5e8248ab26f628db838cbed496f6f5550d27dbd124eb6-%E5%B9%BB%E7%81%AF%E7%89%8711.JPG)

![img](https://pic.leetcode-cn.com/9ed1c9db73ea2ddbfac99e6c59eec877521274de36ef18172c6a8486717cd5d6-%E5%B9%BB%E7%81%AF%E7%89%8712.JPG)

![img](https://pic.leetcode-cn.com/04b0a232c6b1076bf365a013dec4ff5c7868031565fd315fd4d5da4ea3e6c6e7-%E5%B9%BB%E7%81%AF%E7%89%8713.JPG)

![img](https://pic.leetcode-cn.com/a06f6b64e16d6702bc9a6dade557fafbe5c64588e7ac91e275b8313c270ae5f6-%E5%B9%BB%E7%81%AF%E7%89%8714.JPG)

![img](https://pic.leetcode-cn.com/ab2c01e8b33c251d2b21025fa31ca4f2168e9b4f95eb9050fa843296e09f1347-%E5%B9%BB%E7%81%AF%E7%89%8715.JPG)

![img](https://pic.leetcode-cn.com/6bff561d2afc6ea3ddbd74863065eb0e6217066601fb904d1fb1e26c60295b65-%E5%B9%BB%E7%81%AF%E7%89%8716.JPG)

![img](https://pic.leetcode-cn.com/746dffa4bfd97eded8cd9058bdc8450d58b89df2e34c383db7c562e3377f83b2-%E5%B9%BB%E7%81%AF%E7%89%8717.JPG)

![img](https://pic.leetcode-cn.com/8dc1781a7a61da1e0381a5aeb0478ef2f27ea0e06d959f367fec6b34a44ac215-%E5%B9%BB%E7%81%AF%E7%89%8718.JPG)

![img](https://pic.leetcode-cn.com/2dbc7e411b2a01d2ee4b421edd37aa897c15591af41508ad910536b58e1ed950-%E5%B9%BB%E7%81%AF%E7%89%8719.JPG)

![img](https://pic.leetcode-cn.com/bb9451fedecebe1f836fe03bcddba17e009262067f1962bf7fa022c6e2072972-%E5%B9%BB%E7%81%AF%E7%89%8720.JPG)

![img](https://pic.leetcode-cn.com/1e125874e5dae5ebc249cdb89a987aa2e0707ca84215c2fff0c7b0d9926b5d7f-%E5%B9%BB%E7%81%AF%E7%89%8750.JPG)

![img](https://pic.leetcode-cn.com/86beb024dd7e50d1523ee07ffe358ff17374171e2037d97c9349e32076450f73-%E5%B9%BB%E7%81%AF%E7%89%8755.JPG)

## 25、[合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

![image-20210206051612057](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206051612.png)

### 思路

#### 1、递归实现

![image-20210206051813790](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206051813.png)

![recursion.gif](https://pic.leetcode-cn.com/23fbf9388a4193475a7606a6390729f575e3329e0a810d2047682f701d3ddd1f-recursion.gif)

时间复杂度：O(N)*O*(*N*)
空间复杂度：O(h)*O*(*h*)，h*h* 是树的高度

```java
class Solution {
	public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
		if(t1==null || t2==null) {
			return t1==null? t2 : t1;
		}
		return dfs(t1,t2);
	}
	
	TreeNode dfs(TreeNode r1, TreeNode r2) {
		// 如果 r1和r2中，只要有一个是null，函数就直接返回
		if(r1==null || r2==null) {
			return r1==null? r2 : r1;
		}
		//让r1的值 等于  r1和r2的值累加，再递归的计算两颗树的左节点、右节点
		r1.val += r2.val;
		r1.left = dfs(r1.left,r2.left);
		r1.right = dfs(r1.right,r2.right);
		return r1;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-two-binary-trees/solution/dong-hua-yan-shi-di-gui-die-dai-617he-bing-er-cha-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210206052748111](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206052748.png)

#### 2、迭代实现

> 迭代实现用的是广度优先算法，广度优先就需要额外的数据结构来辅助了，我们可以借助栈或者队列来完成。
> 只要两颗树的左节点都不为 null，就把将他们放入队列中；同理只要两棵树的右节点都不为 null 了，也将他们放入队列中。
> 然后我们不断的从队列中取出节点，把他们相加。
> 如果出现 树 1 的 left 节点为 null，树 2 的 left 不为 null，直接将树 2 的 left 赋给树 1 就可以了；同理如果树 1 的 right 节点为 null，树 2 的不为 null，将树 2 的 right 节点赋给树 1。

![iterator.gif](https://pic.leetcode-cn.com/e252bdefa83701034a5c0551b960e6537650d42fd5acfdadcd58a417a985fe37-iterator.gif)

时间复杂度：O(N)*O*(*N*)
空间复杂度：O(N)*O*(*N*)，对于满二叉树时，要保存所有的叶子节点，即 `N/2` 个节点。

```java
class Solution {
	public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
	//如果 t1和t2中，只要有一个是null，函数就直接返回
		if(t1==null || t2==null) {
			return t1==null? t2 : t1;
		}
		java.util.LinkedList<TreeNode> queue = new java.util.LinkedList<TreeNode>();
		queue.add(t1);
		queue.add(t2);
		while(queue.size()>0) {
			TreeNode r1 = queue.remove();
			TreeNode r2 = queue.remove();
			r1.val += r2.val;
			//如果r1和r2的左子树都不为空，就放到队列中
			//如果r1的左子树为空，就把r2的左子树挂到r1的左子树上
			if(r1.left!=null && r2.left!=null){
				queue.add(r1.left);
				queue.add(r2.left);
			}
			else if(r1.left==null) {
				r1.left = r2.left;
			}
			//对于右子树也是一样的
			if(r1.right!=null && r2.right!=null) {
				queue.add(r1.right);
				queue.add(r2.right);
			}
			else if(r1.right==null) {
				r1.right = r2.right;
			}
		}
		return t1;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-two-binary-trees/solution/dong-hua-yan-shi-di-gui-die-dai-617he-bing-er-cha-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210206054149580](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206054149.png)

## 26、[平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

![image-20210206150617143](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206150618.png)

思路

这里强调一波概念：

二叉树节点的深度：指从根节点到该节点的最长简单路径边的条数。
二叉树节点的高度：指从该节点到叶子节点的最长简单路径边的条数。
但leetcode中强调的深度和高度很明显是按照节点来计算的，如图：

![110.平衡二叉树2.png](https://pic.leetcode-cn.com/1601643551-slRuDJ-110.%E5%B9%B3%E8%A1%A1%E4%BA%8C%E5%8F%89%E6%A0%912.png)

关于根节点的深度究竟是1 还是 0，不同的地方有不一样的标准，leetcode的题目中都是以节点为一度，即根节点深度是1。但维基百科上定义用边为一度，即根节点的深度是0，我们暂时以leetcode为准（毕竟要在这上面刷题）。

因为**求深度**可以从上到下去查 所以需要**前序遍历**（中左右），而**高度**只能从下到上去查，所以只能**后序遍历**（左右中）

#### 1、从底至顶（提前阻断）

![image-20210206153935629](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206153936.png)

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return recur(root) != -1;
    }

    private int recur(TreeNode root) {
        if (root == null) return 0;
        int left = recur(root.left);
        if(left == -1) return -1;
        int right = recur(root.right);
        if(right == -1) return -1;
        return Math.abs(left - right) < 2 ? Math.max(left, right) + 1 : -1;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/balanced-binary-tree/solution/balanced-binary-tree-di-gui-fang-fa-by-jin40789108/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210206155013645](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206155013.png)

#### 2、从顶至底（暴力法）

![image-20210206160750863](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210206160753.png)

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        return Math.abs(depth(root.left) - depth(root.right)) <= 1 && isBalanced(root.left) && isBalanced(root.right);
    }

    private int depth(TreeNode root) {
        if (root == null) return 0;
        return Math.max(depth(root.left), depth(root.right)) + 1;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/balanced-binary-tree/solution/balanced-binary-tree-di-gui-fang-fa-by-jin40789108/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![img](https://assets.leetcode-cn.com/solution-static/110/2_1.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_2.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_3.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_4.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_5.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_6.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_7.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_8.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_9.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_10.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_11.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_12.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_13.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_14.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_15.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_16.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_17.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_18.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_19.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_20.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_21.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_22.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_23.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_24.png)

![img](https://assets.leetcode-cn.com/solution-static/110/2_25.png)

![image-20210207000237117](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210207000238.png)

## 27、[特定深度节点链表](https://leetcode-cn.com/problems/list-of-depth-lcci/)

![image-20210207013749169](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210207013750.png)

### 思路

主要是每个链表要怎么移动的问题

这块主要还是二叉树的层序遍历，另外在链表处理的时候，使用了dummy这样的**虚拟节点**，

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode[] listOfDepth(TreeNode tree) {
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.offer(tree);

        List<ListNode> res = new ArrayList<>();
        ListNode dummy = new ListNode(0);
        while (!queue.isEmpty()) {
            int size = queue.size();
            ListNode curr = dummy;
            for (int i = 0; i < size; i++) {
                TreeNode treeNode = queue.poll();
                curr.next = new ListNode(treeNode.val);
                if (treeNode.left != null) {
                    queue.offer(treeNode.left);
                }
                if (treeNode.right != null) {
                    queue.offer(treeNode.right);
                }
                curr = curr.next;
            }
            res.add(dummy.next);
            dummy.next = null;
        }

        return res.toArray(new ListNode[] {});
    }
}

作者：fisher12
链接：https://leetcode-cn.com/problems/list-of-depth-lcci/solution/ceng-xu-bian-li-by-fisher12/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210207014323300](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210207014323.png)

## 28、[N叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

![image-20210208032058304](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210208032100.png)

### 思路

#### 1、迭代

```java
class Solution {
    LinkedList<Node> temp=new LinkedList<>();
    List<Integer> res=new ArrayList<Integer>();
    public List<Integer> preorder(Node root){
        if (root==null) return res;
        temp.add(root);
        while (!temp.isEmpty()){
            Node node = temp.pollLast();
            res.add(node.val);
            for (int i = node.children.size()-1; i >=0 ; i--) {
                if (node.children.get(i)!=null){
                    temp.add(node.children.get(i));
                }
            }
        }
        return res;
    }
}

作者：cztchange
链接：https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/solution/javadie-dai-by-cztchange/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210208033023751](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210208033024.png)

#### 2、**递归**

```java
class Solution {
    List<Integer> res=new ArrayList<Integer>();
    public List<Integer> preorder(Node root) {
        helper(root);
        return res;
    }
    public void helper(Node root){
        if (root==null) return;
        res.add(root.val);
        for (int i = 0; i <root.children.size() ; i++) {
            helper(root.children.get(i));
        }
    }
}

作者：cztchange
链接：https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/solution/javadie-dai-by-cztchange/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 29、[ 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

![image-20210208034505342](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210208034505.png)

### 思路

#### 1、BFS

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    //边界条件判断
    if (root == null)
        return new ArrayList<>();
    //队列
    Queue<TreeNode> queue = new LinkedList<>();
    List<List<Integer>> res = new ArrayList<>();
    //根节点入队
    queue.add(root);
    //如果队列不为空就继续循环
    while (!queue.isEmpty()) {
        //BFS打印，levelNum表示的是每层的结点数
        int levelNum = queue.size();
        //subList存储的是每层的结点值
        List<Integer> subList = new ArrayList<>();
        for (int i = 0; i < levelNum; i++) {
            //出队
            TreeNode node = queue.poll();
            subList.add(node.val);
            //左右子节点如果不为空就加入到队列中
            if (node.left != null)
                queue.add(node.left);
            if (node.right != null)
                queue.add(node.right);
        }
        //把每层的结点值存储在res中，
        res.add(subList);
    }
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/solution/dfshe-bfsliang-chong-fang-shi-jie-jue-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 2、DFS

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    levelHelper(res, root, 0);
    return res;
}

public void levelHelper(List<List<Integer>> list, TreeNode root, int level) {
    //边界条件判断
    if (root == null)
        return;
    //level表示的是层数，如果level >= list.size()，说明到下一层了，所以
    //要先把下一层的list初始化，防止下面add的时候出现空指针异常
    if (level >= list.size()) {
        list.add(new ArrayList<>());
    }
    //level表示的是第几层，这里访问到第几层，我们就把数据加入到第几层
    list.get(level).add(root.val);
    //当前节点访问完之后，再使用递归的方式分别访问当前节点的左右子节点
    levelHelper(list, root.left, level + 1);
    levelHelper(list, root.right, level + 1);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/solution/dfshe-bfsliang-chong-fang-shi-jie-jue-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210208040207434](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210208040207.png)

。

## 30、[最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

![image-20210210233539718](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210210233541.png)

![image-20210210233601978](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210210233602.png)

### 思路

#### 递归

![image-20210210234356821](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210210234357.png)

```java
public class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return construct(nums, 0, nums.length);
    }
    public TreeNode construct(int[] nums, int l, int r) {
        if (l == r)
            return null;
        int max_i = max(nums, l, r);
        TreeNode root = new TreeNode(nums[max_i]);
        root.left = construct(nums, l, max_i);
        root.right = construct(nums, max_i + 1, r);
        return root;
    }
    public int max(int[] nums, int l, int r) {
        int max_i = l;
        for (int i = l; i < r; i++) {
            if (nums[max_i] < nums[i])
                max_i = i;
        }
        return max_i;
    }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/maximum-binary-tree/solution/zui-da-er-cha-shu-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210210234619254](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210210234619.png)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {

    int[] nums;

    public TreeNode constructMaximumBinaryTree(int[] nums) {
        this.nums = nums;
        return helper(0, nums.length);
    }

    public TreeNode helper(int start, int end) {
        //当起始节点大于尾巴节点，说明元素只剩下一个或者更少，就可以终止了
        if (start >= end) {
            return null;
        }
        int max = nums[start];
        int index = start;
        for (int i = start + 1; i < end; i++) {
            if (max < nums[i]) {
                max = nums[i];
                index = i;
            }
        }
        TreeNode root = new TreeNode(max);
        root.left = helper(start, index);
        root.right = helper(index + 1, end);
        return root;
    }


}

作者：1114280204
链接：https://leetcode-cn.com/problems/maximum-binary-tree/solution/cheng-xu-yuan-wu-cheng-feng-ji-bai-100de-di-gui-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 31、[不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

![image-20210212011008564](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210212011009.png)

### 思路

#### 1、递归

![image-20210212013800622](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210212013800.png)

##### 1.1.改进

![image-20210212021230282](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210212021230.png)

![image-20210212015436148](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210212015436.png)



```java
class Solution {
    public int numTrees(int n) {
        int[] record = new int[n + 1];  
        record[0] = 1;  
        return helper(n, record);
    }

    public int helper(int n, int[] record) {
        if (n == 0 || n == 1) {     // 递归出口
            return 1;
        }
        if (record[n] > 0) {        // 如果计算过了
            return record[n];       // 提前返回结束递归求解，省时省空间
        }
        for (int i = 1; i <= n; i++) {
            record[n] += helper(i - 1, record) * helper(n - i, record);
        }
        return record[n];   
    }
}

作者：zui-weng-jiu-xian
链接：https://leetcode-cn.com/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-cong-yuan-shi-de-di-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 2、动态规划

![image-20210213012017909](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213012019.png)

![image-20210213012201257](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213012201.png)

![image-20210213012635217](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213012635.png)



```java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int j = 0; j <= i - 1 ; j++) {
                dp[i] += dp[j] * dp[i - j - 1];
            }
        }
        return dp[n];
    }
}

作者：zui-weng-jiu-xian
链接：https://leetcode-cn.com/problems/unique-binary-search-trees/solution/bu-tong-de-er-cha-sou-suo-shu-cong-yuan-shi-de-di-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```





## 32、[不同的二叉搜索树 II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

![image-20210211015803223](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211015804.png)

### 思路

#### 1、递归

![image-20210211015945962](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211015946.png)

```java
public List<TreeNode> generateTrees(int n) {
    List<TreeNode> ans = new ArrayList<TreeNode>();
    if (n == 0) {
        return ans;
    }
    return getAns(1, n);

}

private List<TreeNode> getAns(int start, int end) { 
    List<TreeNode> ans = new ArrayList<TreeNode>();
    //此时没有数字，将 null 加入结果中
    if (start > end) {
        ans.add(null);
        return ans;
    }
    //只有一个数字，当前数字作为一棵树加入结果中
    if (start == end) {
        TreeNode tree = new TreeNode(start);
        ans.add(tree);
        return ans;
    }
    //尝试每个数字作为根节点
    for (int i = start; i <= end; i++) {
        //得到所有可能的左子树
        List<TreeNode> leftTrees = getAns(start, i - 1);
         //得到所有可能的右子树
        List<TreeNode> rightTrees = getAns(i + 1, end);
        //左子树右子树两两组合
        for (TreeNode leftTree : leftTrees) {
            for (TreeNode rightTree : rightTrees) {
                TreeNode root = new TreeNode(i);
                root.left = leftTree;
                root.right = rightTree;
                //加入到最终结果中
                ans.add(root);
            }
        }
    }
    return ans;
}

作者：windliang
链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-2-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210211021008359](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211021008.png)

#### 2、动态规划

```
考虑 [] 的所有解
null

考虑 [ 1 ] 的所有解
1

考虑 [ 1 2 ] 的所有解
  2
 /
1

 1
  \
   2

考虑 [ 1 2 3 ] 的所有解
    3
   /
  2
 /
1

   2
  / \
 1   3
    
     3
    /
   1
    \
     2
       
   1
     \
      3
     /
    2
    
  1
    \
     2
      \
       3

作者：windliang
链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-2-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

仔细分析，可以发现一个规律。首先我们每次新增加的数字大于之前的所有数字，所以新增加的数字出现的位置只可能是根节点或者是根节点的右孩子，右孩子的右孩子，右孩子的右孩子的右孩子等等，总之一定是右边。其次，新数字所在位置原来的子树，改为当前插入数字的左孩子即可，因为插入数字是最大的。

```json
对于下边的解 
  2
 /
1

然后增加 3
1.把 3 放到根节点
    3
   /
  2
 /
1

2. 把 3 放到根节点的右孩子
   2
  / \
 1   3
 
对于下边的解
 1
  \
   2

然后增加 3
1.把 3 放到根节点
     3
    /
   1
    \
     2
       
2. 把 3 放到根节点的右孩子，原来的子树作为 3 的左孩子       
      1
        \
         3
        /
      2

3. 把 3 放到根节点的右孩子的右孩子
  1
    \
     2
      \
       3

作者：windliang
链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-2-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

以上就是根据 [ 1 2 ] 推出 [ 1 2 3 ] 的所有过程，可以写代码了。由于求当前的所有解只需要上一次的解，所有我们只需要两个 list，pre 保存上一次的所有解， cur 计算当前的所有解。

```java
public List<TreeNode> generateTrees(int n) {
    List<TreeNode> pre = new ArrayList<TreeNode>();
    if (n == 0) {
        return pre;
    }
    pre.add(null);
    //每次增加一个数字
    for (int i = 1; i <= n; i++) {
        List<TreeNode> cur = new ArrayList<TreeNode>();
        //遍历之前的所有解
        for (TreeNode root : pre) {
            //插入到根节点
            TreeNode insert = new TreeNode(i);
            insert.left = root;
            cur.add(insert);
            //插入到右孩子，右孩子的右孩子...最多找 n 次孩子
            for (int j = 0; j <= n; j++) {
                TreeNode root_copy = treeCopy(root); //复制当前的树
                TreeNode right = root_copy; //找到要插入右孩子的位置
                int k = 0;
                //遍历 j 次找右孩子
                for (; k < j; k++) {
                    if (right == null)
                        break;
                    right = right.right;
                }
                //到达 null 提前结束
                if (right == null)
                    break;
                //保存当前右孩子的位置的子树作为插入节点的左孩子
                TreeNode rightTree = right.right;
                insert = new TreeNode(i);
                right.right = insert; //右孩子是插入的节点
                insert.left = rightTree; //插入节点的左孩子更新为插入位置之前的子树
                //加入结果中
                cur.add(root_copy);
            }
        }
        pre = cur;

    }
    return pre;
}


private TreeNode treeCopy(TreeNode root) {
    if (root == null) {
        return root;
    }
    TreeNode newRoot = new TreeNode(root.val);
    newRoot.left = treeCopy(root.left);
    newRoot.right = treeCopy(root.right);
    return newRoot;
}

作者：windliang
链接：https://leetcode-cn.com/problems/unique-binary-search-trees-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-2-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210211234101801](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211234103.png)

![image-20210211234235403](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211234235.png)

![image-20210211234801687](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210211234801.png)

## 33、[盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

![image-20210213024035335](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213024036.png)

### 思路

#### 双指针法

```java
public int maxArea(int[] height) {
    int res = 0;
    int i = 0;
    int j = height.length - 1;
    while (i < j) {
        int area = (j - i) * Math.min(height[i], height[j]);
        res = Math.max(res, area);
        if (height[i] < height[j]) {
            i++;
        } else {
            j--;
        }
    }
    return res;
}

作者：nettee
链接：https://leetcode-cn.com/problems/container-with-most-water/solution/on-shuang-zhi-zhen-jie-fa-li-jie-zheng-que-xing-tu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

> 这道题目看似简单，做起来才发现不容易。分治法、动态规划都用不上，要想得到 O(n)O(n) 的解法只有使用双指针一条路。即使看了答案知道了双指针解法，你也可能并不清楚这个解法为什么正确。**为什么双指针往中间移动时，不会漏掉某些情况呢**？
>
> 如果没有真正理解题目，即使一次对着答案做出来了，再次遇到这个题目，还是可能做不出来。要理解这道题的正确性和原理，需要从背后的缩减搜索空间的思想去考虑题解。下面我将用图片解释这道题的正确性和原理。

![image-20210213024536712](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213024537.png)

![image-20210213024625783](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213024626.png)

#### 图解双指针解法的原理

![image-20210213024817896](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213024818.png)

![image-20210213025313079](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213025313.png)

![image-20210213025357431](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213025357.png)

## 34、[三数之和](https://leetcode-cn.com/problems/3sum/)

![image-20210213162123819](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213162124.png)

### 思路

#### 双指针法

![image-20210213162401201](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213162401.png)

![image-20210213162555090](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213162555.png)

其中：在第 6 行时，因为三数之和大于 0，所以 right 进行了左移。最后一行，跳过了重复的 -1。

然后啰嗦一句，因为我们需要**处理重复值**的情况。除了固定下来的i值（蓝框框），**left 和 right** 当然也是需要处理重复的情况，所以对于 left 和 left+1，以及 right 和 right-1，我们都单独做一下重复值的处理。（其实没啥处理，就是简单的跳过）

```java
//java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList();
        for (int i = 0; i < nums.length; i++) {
            int target = 0 - nums[i];
            int l = i + 1;
            int r = nums.length - 1;
            if (nums[i] > 0)
                break;
            if (i == 0 || nums[i] != nums[i - 1]) {
                while (l < r) {
                    if (nums[l] + nums[r] == target) {
                        res.add(Arrays.asList(nums[i], nums[l], nums[r]));
                        while (l < r && nums[l] == nums[l + 1]) l++;
                        while (l < r && nums[r] == nums[r - 1]) r--;
                        l++;
                        r--;
                    } else if (nums[l] + nums[r] < target)
                        l++;
                    else
                        r--;
                }
            }
        }
        return res;
    }
}

作者：ivan1
链接：https://leetcode-cn.com/problems/3sum/solution/man-hua-jue-bu-wu-ren-zi-di-xiang-kuai-su-kan-dong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210213163218807](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213163219.png)

![image-20210213163234413](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213163234.png)

## 35、[最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

![image-20210213171739192](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213171739.png)

### 思路

#### 双指针

![image-20210213172104549](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210213172104.png)

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int ans = nums[0] + nums[1] + nums[2];
        for(int i=0;i<nums.length;i++) {
            int start = i+1, end = nums.length - 1;
            while(start < end) {
                int sum = nums[start] + nums[end] + nums[i];
                if(Math.abs(target - sum) < Math.abs(target - ans))
                    ans = sum;
                if(sum > target)
                    end--;
                else if(sum < target)
                    start++;
                else
                    return ans;
            }
        }
        return ans;
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/3sum-closest/solution/hua-jie-suan-fa-16-zui-jie-jin-de-san-shu-zhi-he-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 36、[四数之和](https://leetcode-cn.com/problems/4sum/)

![image-20210214231846141](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210214231847.png)

### 思路

#### 双指针

```java
public List<List<Integer>> fourSum(int[] nums,int target){
        /*定义一个返回值*/
        List<List<Integer>> result=new ArrayList<>();
        /*当数组为null或元素小于4个时，直接返回*/
        if(nums==null||nums.length<4){
            return result;
        }
        /*对数组进行从小到大排序*/
        Arrays.sort(nums);
        /*数组长度*/
        int length=nums.length;
        /*定义4个指针k，i，j，h  k从0开始遍历，i从k+1开始遍历，留下j和h，j指向i+1，h指向数组最大值*/
        for(int k=0;k<length-3;k++){
            /*当k的值与前面的值相等时忽略*/
            if(k>0&&nums[k]==nums[k-1]){
                continue;
            }
            /*获取当前最小值，如果最小值比目标值大，说明后面越来越大的值根本没戏*/
            int min1=nums[k]+nums[k+1]+nums[k+2]+nums[k+3];
            if(min1>target){
                break;
            }
            /*获取当前最大值，如果最大值比目标值小，说明后面越来越小的值根本没戏，忽略*/
            int max1=nums[k]+nums[length-1]+nums[length-2]+nums[length-3];
            if(max1<target){
                continue;
            }
            /*第二层循环i，初始值指向k+1*/
            for(int i=k+1;i<length-2;i++){
                /*当i的值与前面的值相等时忽略*/
                if(i>k+1&&nums[i]==nums[i-1]){
                    continue;
                }
                /*定义指针j指向i+1*/
                int j=i+1;
                /*定义指针h指向数组末尾*/
                int h=length-1;
                /*获取当前最小值，如果最小值比目标值大，说明后面越来越大的值根本没戏*/
                int min=nums[k]+nums[i]+nums[j]+nums[j+1];
                if(min>target){
                    break;
                }
                /*获取当前最大值，如果最大值比目标值小，说明后面越来越小的值根本没戏，忽略*/
                int max=nums[k]+nums[i]+nums[h]+nums[h-1];
                if(max<target){
                    continue;
                }
                /*开始j指针和h指针的表演，计算当前和，如果等于目标值，j++并去重，h--并去重，当当前和大于目标值时h--，当当前和小于目标值时j++*/
                while (j<h){
                    int curr=nums[k]+nums[i]+nums[j]+nums[h];
                    if(curr==target){
                        result.add(Arrays.asList(nums[k],nums[i],nums[j],nums[h]));
                        j++;
                        while(j<h&&nums[j]==nums[j-1]){
                            j++;
                        }
                        h--;
                        while(j<h&&i<h&&nums[h]==nums[h+1]){
                            h--;
                        }
                    }else if(curr>target){
                        h--;
                    }else {
                       j++;
                    }
                }
            }
        }
        return result;
    }

作者：you-wei-wu
链接：https://leetcode-cn.com/problems/4sum/solution/ji-bai-9994de-yong-hu-you-dai-ma-you-zhu-shi-by-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210214232651972](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210214232652.png)



## 37、[删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

![image-20210214233456574](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210214233457.png)

### 思路

#### 双指针（快慢指针）

![img](https://pic.leetcode-cn.com/cc43daa8cbb755373ce4c5cd10c44066dc770a34a6d2913a52f8047cbf5e6e56-file_1559548337458)

![image-20210214233704561](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210214233704.png)

使用快慢指针，快指针先移 n 个节点。

接下来，快慢指针一起移动，两指针之间一直保持 n 个节点，当快指针到链表底了，操作慢指针，删除要删除的元素！

采用两个间隔为n的指针，同时向前移动。当快指针的下一个节点为最后一个节点时，要删除的节点就是慢指针的下一个节点。

时间复杂度：O(n)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode slow = dummy;
        ListNode fast = dummy;
        for (int i = 0 ; i < n; i ++ ){
            fast = fast.next;
        }
        while(fast.next != null){
            slow = slow.next;
            fast = fast.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
        
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/solution/kuai-man-zhi-zhen-by-powcai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 计算链表长度

![image-20210215000153813](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215000154.png)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        int length = getLength(head);
        ListNode cur = dummy;
        for (int i = 1; i < length - n + 1; ++i) {
            cur = cur.next;
        }
        cur.next = cur.next.next;
        ListNode ans = dummy.next;
        return ans;
    }

    public int getLength(ListNode head) {
        int length = 0;
        while (head != null) {
            ++length;
            head = head.next;
        }
        return length;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/solution/shan-chu-lian-biao-de-dao-shu-di-nge-jie-dian-b-61/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

**复杂度分析**

- 时间复杂度：O(L)，其中 L是链表的长度。
- 空间复杂度：O(1)。

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode pre = head;
    int last = length(head) - n;
    //如果last等于0表示删除的是头结点
    if (last == 0)
        return head.next;
    //这里首先要找到要删除链表的前一个结点
    for (int i = 0; i < last - 1; i++) {
        pre = pre.next;
    }
    //然后让前一个结点的next指向要删除节点的next
    pre.next = pre.next.next;
    return head;
}

//求链表的长度
private int length(ListNode head) {
    int len = 0;
    while (head != null) {
        len++;
        head = head.next;
    }
    return len;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/solution/shuang-zhi-zhen-di-gui-deng-3chong-jie-jue-fang-sh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210215134816941](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215134819.png)

#### 递归解决

![image-20210215134919043](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215134919.png)

## 38、[删除排序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

![image-20210215214401330](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215214402.png)

### 思路

#### 双指针

![image-20210215214513793](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215214514.png)

![image-20210215214851286](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215214851.png)

```java
 public int removeDuplicates(int[] nums) {
    if(nums == null || nums.length == 0) return 0;
    int p = 0;
    int q = 1;
    while(q < nums.length){
        if(nums[p] != nums[q]){
            nums[p + 1] = nums[q];
            p++;
        }
        q++;
    }
    return p + 1;
}

作者：max-LFszNScOfE
链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/solution/shuang-zhi-zhen-shan-chu-zhong-fu-xiang-dai-you-hu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度

时间复杂度：O(n)。
空间复杂度：O(1)。

![image-20210215215503729](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215215504.png)

## 39、[移除元素](https://leetcode-cn.com/problems/remove-element/)

![image-20210215224514341](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215224515.png)

### 思路

#### 双指针

![image-20210215224612167](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215224612.png)

```java
复杂度分析public int removeElement(int[] nums, int val) {
    int i = 0;
    for (int j = 0; j < nums.length; j++) {
        if (nums[j] != val) {
            nums[i] = nums[j];
            i++;
        }
    }
    return i;
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/remove-element/solution/yi-chu-yuan-su-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### **复杂度分析**

![image-20210215224654889](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210215224655.png)

#### 优化双指针

![image-20210216012331008](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216012332.png)

##### **复杂度分析**

![image-20210216012441590](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216012441.png)

## 40、[实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

![image-20210216013713751](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216013714.png)

### 思路

![image-20210216020607592](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216020608.png)

#### BF算法（Brute Force）

这个算法很容易理解，就是我们将模式串和主串进行比较，一致时则继续比较下一字符，直到比较完整个模式串。不一致时则将模式串后移一位，重新从模式串的首位开始对比，重复刚才的步骤下面我们看下这个方法的动图解析，看完肯定一下就能搞懂啦。

![image-20210216020840675](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216020841.png)

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int haylen = haystack.length();
        int needlen = needle.length(); 
        //特殊情况
        if (haylen < needlen) {
            return -1;
        }
        if (needlen == 0) {
            return 0;
        }
        //主串
        for (int i = 0; i < haylen - needlen + 1; ++i) {
            int j;
            //模式串
            for (j = 0; j < needlen; j++) {
                //不符合的情况，直接跳出，主串指针后移一位
                if (haystack.charAt(i+j) != needle.charAt(j)) {
                    break;
                }
            }
            //匹配成功
            if (j == needlen) {
                return i;
            } 

        }
        return -1;
    }
}

作者：chefyuan
链接：https://leetcode-cn.com/problems/implement-strstr/solution/zhe-ke-neng-shi-quan-wang-zui-xi-de-kmp-8zl57/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### KMP算法

找出公共的前后缀，用前缀替代后缀的位置，因为后缀肯定跟主串已经是最大的匹配了，此时替换后直接从前缀的下一个字符开始比较

KMP 算法是一种改进的字符串模式匹配的算法，它的时间复杂度为 `O(n+m)`，其核心思想是**当出现不匹配的字符时，不需要回溯主串的指针，而是利用已经得到的“部分匹配”，将模式字符串尽可能多地向右移动，然后重新比较。**

KMP 算法的核心是一个被称为部分匹配表（Partial Match Table）的数组。比如字符串 `abababca`，它的 PMT 如下：

![image-20201220153222385](https://gitee.com/top20chenql/md_imgs/raw/master/img/20201220153225.png)

在解释这个表之前，首先需要解释一下字符串的前缀和后缀。比如字符串 `Saber`，它的前缀包括 `S`、`Sa`、`Sab` 和 `Sabe`，它的后缀包括 `r`、`er`、`ber` 和 `aber`。字符串本身不是自己的前缀或后缀。

有了前缀和后缀的定义，就可以说明 PMT 中每个值的意义了。**PMT 中的值是字符串的前缀集合和后缀集合的交集中最长元素的长度。**比如，字符串 `aba`，它的前缀集合为 `a` 和 `ab`，它的后缀集合为 `a`、`ba`，两个集合的交集为 `a`，那么最长的元素的长度也就是 1。那么对于字符串 `abababca`，也就是在上表中，pmt[0] 的值为 0，pmt[1] 的值也是 0，pmt[2] 的值为 1，以此类推。

比如要在字符串 `ababababca` 中查找字符串 `abababca`，如果在指针 j（或者 i）处字符不匹配，那么**主串 i 指针之前的 PMT[j - 1] 位就一定与模式字符串的第 0 位到第 PMT[j - 1] 位是相同的**。具体来说，因为在指针 j（或者 i）处不匹配，所以主串从 i - j 到 i 之前这段与模式字符串的 0 到 j 之前这段是完全相同的，在这个例子中就是 `ababab` 这段，它的前缀集合和后缀集合的交集的最长元素为 `abab`，长度为 4。所以可以说，主串 i 指针之前的 4 位与模式字符串的第 0 位到第 4 位是相同的，这样我们就可以省略掉这些字符的比较，保持 i 指针不动，将 j 指针指向模式字符串的 PMT[j - 1] 位（也就是第 4 位）即可。

![image-20201220155711588](https://gitee.com/top20chenql/md_imgs/raw/master/img/20201220155711.png)

从上面可以看出，如果在 j 位置失配，那么 j 指针回溯的位置其实是第 j - 1 位置的 PMT 的值。为了编程方便（没有其他意义），我们将 PMT 数组整体向右偏移一位（其中第一位始终为 -1），我们把新得到的数组称为 next 数组。



接下来就是如何通过编码求得 next 数组了。求 next 数组的过程完全可以看成字符串匹配的过程，即以模式字符串为主串，以模式字符串的前缀为目标字符串，从模式字符串的第一位（不包括第 0 位）开始对自身进行匹配，在任一位置，能匹配的最长长度就是当前位置的 next 值。



```java
class Solution {
    public int strStr(String haystack, String needle) {
        //两种特殊情况
        if (needle.length() == 0) {
            return 0;
        }
        if (haystack.length() == 0) {
            return -1;
        }
        // char 数组
        char[] hasyarr = haystack.toCharArray();
        char[] nearr = needle.toCharArray();
        //长度
        int halen = hasyarr.length;
        int nelen = nearr.length;
        //返回下标
        return kmp(hasyarr,halen,nearr,nelen);

    }
    public int kmp (char[] hasyarr, int halen, char[] nearr, int nelen) {
        //获取next 数组
        int[] next = next(nearr,nelen);
        int j = 0;
        for (int i = 0; i < halen; ++i) {
            //发现不匹配的字符，然后根据 next 数组移动指针，移动到最大公共前后缀的，
            //前缀的后一位,和咱们移动模式串的含义相同
            while (j > 0 && hasyarr[i] != nearr[j]) {
                j = next[j - 1] + 1;
                //超出长度时，可以直接返回不存在
                if (nelen - j + i > halen) {
                    return -1;
                }
            }
            //如果相同就将指针同时后移一下，比较下个字符
            if (hasyarr[i] == nearr[j]) {
                ++j;
            }
            //遍历完整个模式串，返回模式串的起点下标
            if (j == nelen) {
                return i - nelen + 1;
            }
        }
        return -1;
    }
    //这一块比较难懂，不想看的同学可以忽略，了解大致含义即可，或者自己调试一下，看看运行情况
    //我会每一步都写上注释
    public  int[] next (char[] needle,int len) {
        //定义 next 数组
        int[] next = new int[len];
        // 初始化
        next[0] = -1;
        int k = -1;
        for (int i = 1; i < len; ++i) {
            //我们此时知道了 [0,i-1]的最长前后缀，但是k+1的指向的值和i不相同时，我们则需要回溯
            //因为 next[k]就时用来记录子串的最长公共前后缀的尾坐标（即长度）
            //就要找 k+1前一个元素在next数组里的值,即next[k+1]
            while (k != -1 && needle[k + 1] != needle[i]) {
                k = next[k];
            }
            // 相同情况，就是 k的下一位，和 i 相同时，此时我们已经知道 [0,i-1]的最长前后缀
            //然后 k - 1 又和 i 相同，最长前后缀加1，即可
            if (needle[k+1] == needle[i]) {
                ++k;
            }
            next[i] = k;

        }
        return next;
    }
}

作者：chefyuan
链接：https://leetcode-cn.com/problems/implement-strstr/solution/zhe-ke-neng-shi-quan-wang-zui-xi-de-kmp-8zl57/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210216174415439](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216174417.png)

##### 构造next数组

**构造next数组其实就是计算模式串s，前缀表的过程。** 主要有如下三步：

1. 初始化
2. 处理前后缀不相同的情况
3. 处理前后缀相同的情况

![image-20210216175621797](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210216175622.png)

**next 数组**

next数组到底是个什么鬼呢？这是一个一维整型数组，数组的下标代表了“已匹配前缀的下一个位置”，元素的值则是“最长可匹配前缀子串的下一个位置”。

或许这样的描述有些晦涩，我们来看一下图：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/ab653cfea2d840c099d4f8a5e02da3ff.png)

当模式串的第一个字符就和主串不匹配时，并不存在已匹配前缀子串，更不存在 最长可匹配前缀子串。这种情况对应的next数组下标是0，next[0]的元素值也是0。

如果已匹配前缀是G、GT、GTGTGC，并不存在最长可匹配前缀子串，所以对应的next数组元素值（next[1]，next[2]，next[6]）同样是0。

GTG的最长可匹配前缀是G，对应数组中的next[3]，元素值是1。

以此类推，

GTGT 对应 next[4]，元素值是2。

GTGTG 对应 next[5]，元素值是3。

![image-20210218002935127](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210218002935.png)

假设模式串的长度是m，生成next数组所需的最大总比较次数是1+2+3+4+......+m-2 次。

显然，这种方法的效率非常低，如何进行优化呢？

我们可以采用类似“动态规划”的方法。首先next[0]和next[1]的值肯定是0，因为这时候不存在前缀子串；从next[2]开始，next数组的每一个元素都可以由上一个元素推导而来。

已知next[i]的值，如何推导出next[i+1]呢？让我们来演示一下上述next数组的填充过程：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/49f773e98c644ce8976bc4f2640be443.png)

如图所示，我们设置两个变量i和j，其中i表示“ **已**匹配前缀的下一个位置”，也就是待填充的数组下标，**j表示 “最长可匹配前缀子串的下一个位置”**，也就是待填充的数组元素值。

当已匹配前缀不存在的时候，最长可匹配前缀子串当然也不存在，所以i=0，j=0，此时 **next[0] = 0**。

接下来，我们让已匹配前缀子串的长度加1：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/2ca4fdd0c4624792b2374f62f0af1fe8.png)

此时的已匹配前缀是G，由于只有一个字符，同样不存在最长可匹配前缀子串，所以i=1，j=0， **next[1] = 0**。

接下来，我们让已匹配前缀子串的长度继续加1：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/057769134e314eeba89a7b00bd33453a.png)

此时的已匹配前缀是GT，我们需要开始做判断了：由于模式串当中 **pattern[j] != pattern[i-1]**，即G！=T，最长可匹配前缀子串仍然不存在。

![image-20210218003255311](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210218003255.png)

接下来，我们让已匹配前缀子串的长度继续加1：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/e9daec88e0f448158554c2a1d893f940.png)

此时的已匹配前缀是GTGT，由于模式串当中 **pattern[j] = pattern[i-1]**，即T=T，最长可匹配前缀子串又增加了一位，是GT。

所以当i=4时，j=2， **next[4] = next[3]+1 = 2**。

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/e42734214cb34b51a87fd94063b55b25.png)

接下来，我们让已匹配前缀子串的长度继续加1：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/ffe5bfce71a9466d8d42c1dbc3fd6d7a.png)

此时的已匹配前缀是GTGTG，由于模式串当中 **pattern[j] = pattern[i-1]**，即G=G，最长可匹配前缀子串又增加了一位，是GTG。

所以当i=5时，j=3，**next[5] = next[4]+1 = 3**。

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/be705865b2c0447793942ebeb5d2384a.png)

接下来，我们让已匹配前缀子串的长度继续加1：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/6d0365ea1c424385b5ea8f20025aad93.png)

此时的已匹配前缀是GTGTGC，这时候需要注意了，模式串当中 **pattern[j] ！= pattern[i-1]**，即T != C，这时候该怎么办呢？

这时候，我们已经无法从next[5]的值来推导出next[6]，而字符C的前面又有两段重复的子串“GTG”。那么，我们能不能把问题转化一下？

或许听起来有些绕：我们可以把计算“GTGTGC”最长可匹配前缀子串的问题，转化成计算“GTGC”最长可匹配前缀子串的问题。

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/ada4f9ad1754459f953ae0deb181d909.png)

这样的问题转化，也就相当于把变量j回溯到了next[j]，也就是j=1的局面（i值不变）：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/e3e6e771157f48a28fa684028e2ab718.png)

回溯后，情况仍然是 **pattern[j] ！= pattern[i-1]**，即T！=C。那么我们可以把问题继续进行转化：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/259c5c2ff8c54981a6c64ee779bedd08.png)

问题再次的转化，相当于再一次把变量j回溯到了next[j]，也就是j=0的局面：

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/4b4ce55eacc940e3b8ace6001f2f810a.png)

回溯后，情况仍然是 **pattern[j] ！= pattern[i-1]**，即G！=C。j已经不能再次回溯了，所以我们得出结论：i=6时，j=0， **next[6] = 0**。

![img](http://5b0988e595225.cdn.sohucs.com/images/20200226/7dd715535c2f4ae1a0bf62a4d801420d.png)

以上就是next数组元素的推导过程。

##### 暴力算法的指针回溯

![image-20210218005859725](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210218005900.png)

##### 详解（代码结合图理解）

https://blog.csdn.net/haazzz/article/details/109098262

```java
 private static int[] Get_Next(String T){

        int[] next = new int[T.length()];
        int j = 0,k = -1;

        next[0] = -1;

        while(j < T.length()-1){

            if( k==-1|| T.charAt(j)==T.charAt(k)){
                ++j;
                ++k;
                next[j] = k;
            }
            else
                k = next[k];
        }
        return next;
    }

    public static int Index_KMP(String S,int pos,String T){

        int[] next = Get_Next(T);

        int i = pos,j = 0;
        while(i < S.length() && j < T.length()){
            if( j==-1 || S.charAt(i)==T.charAt(j) ){
                ++i;
                ++j;
            }
            else{
                j = next[j];
            }
        }
        if(j == T.length())
            return i-T.length();
        else
            return 0;
    }

```



![image-20210219013649805](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219013651.png)

![image-20210219013750357](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219013750.png)

![image-20210219013914758](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219013915.png)

![image-20210219014342691](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219014343.png)

![image-20210219014518659](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219014518.png)

![image-20210219015126185](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219015126.png)

![image-20210219015159787](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219015200.png)

![image-20210219021730332](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219021730.png)

```java
    private int indexOf(char[] source, char[] pattern) {
        int[] next = getNext(pattern);
        int i = 0;
        int j = 0;
        while (i < source.length && j < pattern.length) {
            if (j == -1 || source[i] == pattern[j]) {
                i++;
                j++;
            } else {
                j = next[j];
            }
        }
 
        if (j == pattern.length) {
            return i - j;
        }
        return -1;
    }
 
    private int[] getNext(char[] chars) {
        int[] next = new int[chars.length];
        int j = -1;
        int i = 0;
        next[0] = -1;
        while (i < chars.length - 1) {
            if (j == -1 || chars[i] == chars[j]) {
                next[++i] = ++j;
            } else {
                j = next[j];
            }
        }
        return next;
    }
```

###### 移动问题思路分析

https://blog.csdn.net/haazzz/article/details/109098262

![image-20210219022212356](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219022212.png)

![image-20210219022818918](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219022819.png)

![image-20210219023026434](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219023026.png)

![image-20210219023048198](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219023048.png)

![image-20210219023308533](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219023309.png)

![image-20210219023903652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219023904.png)

###### next数组详讲

![image-20210219024058834](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219024059.png)

![image-20210219024132282](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219024132.png)

![image-20210219024537654](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219024537.png)

![image-20210219025414322](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219025414.png)

![image-20210219025952898](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210219025953.png)

```java
int[] getNext(String pattern) {
        int[] next = new int[pattern.length()];
        next[0] = -1;
        int i=0, j=-1;

        while(i < pattern.length()-1) {
            if(j == -1 || pattern.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
                next[i] = j;
            } else {
                j = next[j];
            }
        }

        return next;
    }
```

```java
    /**
     * KMP匹配字符串
     * @param source  源字符串
     * @param subStr  模式字符串
     * @return
     */
    int kmpIndexOf(String target, String pattern) {
        int[] next = getNext(pattern);
        int i=0, j=0;

        while(i < target.length() && j < pattern.length()) {
            //j==-1是关键，表示已经回溯到了第0位，这就是在上面推导next数组时使用-1的原因
            if(j==-1 || target.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            } else {
                j = next[j];
            }
        }

        if(j >= target.length()) {
            return i - target.length();
        }

        return -1;
    }
```



###### nextval数组



## 41、[搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

![image-20210313171551778](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313171554.png)

### 思路

![image-20210313171739487](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313171739.png)

#### 二分查找

###### 原理

> 二分查找也称折半查找（Binary Search），是一种在有序数组中查找某一特定元素的搜索算法。我们可以从定义可知，运用二分搜索的前提是数组必须是有序的，这里需要注意的是，我们的输入不一定是数组，也可以是数组中某一区间的起始位置和终止位置

![image-20210313181708323](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313181709.png)

![image-20210313181802109](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313181802.png)

![image-20210313181920809](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313181921.png)

![image-20210313182058144](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313182058.png)

> 二分查找的执行过程如下
>
> 1.从已经排好序的数组或区间中，取出中间位置的元素，将其与我们的目标值进行比较，判断是否相等，如果相等
>
> 则返回。
>
> 2.如果 nums[mid] 和 target 不相等，则对 nums[mid] 和 target 值进行比较大小，通过比较结果决定是从 mid
>
> 的左半部分还是右半部分继续搜索。如果 target > nums[mid] 则右半区间继续进行搜索，即 left = mid + 1; 若
>
> target < nums[mid] 则在左半区间继续进行搜索，即 right = mid -1；
>
> 作者：yuan-chu-de-suan-fa-xiao-wu
> 链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/yi-wen-dai-ni-shua-bian-er-fen-cha-zhao-dtadq/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

![二分查找2](https://pic.leetcode-cn.com/1608987789-UOOWmi-file_1608987789042)

###### 代码

下面我们来看一下二分查找的代码，可以认真思考一下 if 语句的条件，每个都没有简写。

```java
 public static int binarySearch(int[] nums,int target,int left, int right) {
        //这里需要注意，循环条件
        while (left <= right) {
            //这里需要注意，计算mid
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target) {
                return mid;
            }else if (nums[mid] < target) {
                //这里需要注意，移动左指针
                left  = mid + 1;
            }else if (nums[mid] > target) {
                //这里需要注意，移动右指针
                right = mid - 1;
            }
        }
        //没有找到该元素，返回 -1
        return -1;
    }

作者：yuan-chu-de-suan-fa-xiao-wu
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/yi-wen-dai-ni-shua-bian-er-fen-cha-zhao-dtadq/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

二分查找的思路及代码已经理解了，那么我们来看一下实现时容易出错的地方

> 1.计算 mid 时 ，不能使用 （left + right ）/ 2,否则有可能会导致溢出
>
> 2.while (left < = right) { } 注意括号内为 left <= right ,而不是 left < right ，我们继续回顾刚才的例子，如果我们设置条件为 left < right 则当我们执行到最后一步时，则我们的 left 和 right 重叠时，则会跳出循环，返回 -1，区间内不存在该元素，但是不是这样的，我们的 left 和 right 此时指向的就是我们的目标元素 ，但是此时 left = right 跳出循环
>
> 3.left = mid + 1,right = mid - 1 而不是 left = mid 和 right = mid。我们思考一下这种情况,见下图，当我们的target 元素为 16 时，然后我们此时 left = 7 ，right = 8，mid = left + (right - left) = 7 + (8-7) = 7，那如果设置 left = mid 的话，则会进入死循环，mid 值一直为7 。

![二分查找出差](https://pic.leetcode-cn.com/1608987789-pFHWdA-file_1608987789043)

下面我们来看一下二分查找的递归写法

```java
public static int binarySearch(int[] nums,int target,int left, int right) {
        
        if (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target) {
                //查找成功
                return  mid;
            }else if (nums[mid] > target) {
                //新的区间,左半区间
                return binarySearch(nums,target,left,mid-1);
            }else if (nums[mid] < target) {
                //新的区间，右半区间
                return binarySearch(nums,target,mid+1,right);
            }
        }
        //不存在返回-1
        return -1;
    }

作者：yuan-chu-de-suan-fa-xiao-wu
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/yi-wen-dai-ni-shua-bian-er-fen-cha-zhao-dtadq/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### 二分法解本题

```java
public int search(int[] nums, int target) {
    int lo = 0, hi = nums.length - 1, mid = 0;
    while (lo <= hi) {
        mid = lo + (hi - lo) / 2;
        if (nums[mid] == target) {
            return mid;
        }
        // 先根据 nums[mid] 与 nums[lo] 的关系判断 mid 是在左段还是右段 
        if (nums[mid] >= nums[lo]) {
            // 再判断 target 是在 mid 的左边还是右边，从而调整左右边界 lo 和 hi
            if (target >= nums[lo] && target < nums[mid]) {
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        } else {
            if (target > nums[mid] && target <= nums[hi]) {
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
    }
    return -1;
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/duo-si-lu-wan-quan-gong-lue-bi-xu-miao-dong-by-swe/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 42、[在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

![image-20210313184249640](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210313184250.png)

### 思路

#### 二分查找

##### 二分查找的三种写法

![image-20210314001640943](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314001642.png)

##### 第 1 部分：查找 target 出现的第 1 个位置

![image-20210314001817101](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314001817.png)

```java
private int findFirstPosition(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            // ① 不可以直接返回，应该继续向左边找，即 [left..mid - 1] 区间里找
            right = mid - 1;
        } else if (nums[mid] < target) {
            // 应该继续向右边找，即 [mid + 1, right] 区间里找
            left = mid + 1;
        } else {
            // 此时 nums[mid] > target，应该继续向左边找，即 [left..mid - 1] 区间里找
            right = mid - 1;
        }
    }

    // 此时 left 和 right 的位置关系是 [right, left]，注意上面的 ①，此时 left 才是第 1 次元素出现的位置
    // 因此还需要特别做一次判断
    if (left != nums.length && nums[left] == target) {
        return left;
    }
    return -1;
}

作者：yong-cai-gu-zi-xi
链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/da-jia-bu-yao-kan-labuladong-de-jie-fa-fei-chang-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **解释**：

第 11 次出现的位置和最后 11 次出现的位置肯定都在数组里。因此，初始化的时候 left = 0 、 right = nums.length - 1；
nums[mid] == target 的时候，在 [left, mid - 1] 区间里找，有没有可能 nums[mid] 就是第 11 次出现的位置，有可能，但不要紧，**退出循环的时候 right 指针在左，left 在右**。如果数组里存在 target，那么 left 一定位于 target 出现的第 11 个位置，请看下图。

![image.png](https://pic.leetcode-cn.com/1598625222-ESDFZS-image.png)

还有一种特殊情况，当要查找的目标元素不存在的时，分两种情况：（1）target 很大，（2）target 很小：

![image-20210314003755911](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314003757.png)

##### 第 2 部分：查找 target 出现的最后 1 个位置

可以直接看注释。

###### 代码

```java
private int findLastPosition(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            // 只有这里不一样：不可以直接返回，应该继续向右边找，即 [mid + 1, right] 区间里找
            left = mid + 1;
        } else if (nums[mid] < target) {
            // 应该继续向右边找，即 [mid + 1, right] 区间里找
            left = mid + 1;
        } else {
            // 此时 nums[mid] > target，应该继续向左边找，即 [left, mid - 1] 区间里找
            right = mid - 1;
        }
    }
    // 由于 findFirstPosition 方法可以返回是否找到，这里无需单独再做判断
    return right;
}

作者：yong-cai-gu-zi-xi
链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/da-jia-bu-yao-kan-labuladong-de-jie-fa-fei-chang-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210314003953371](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314003953.png)

接下来，我们补上主调方法：

```java
public int[] searchRange(int[] nums, int target) {
    if (nums.length == 0) {
        return new int[]{-1, -1};
    }
    int firstPosition = findFirstPosition(nums, target);
    // 如果第 1 次出现的位置都找不到，肯定不存在最后 1 次出现的位置
    if (firstPosition == -1) {
        return new int[]{-1, -1};
    }
    int lastPosition = findLastPosition(nums, target);
    return new int[]{firstPosition, lastPosition};
}

作者：yong-cai-gu-zi-xi
链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/da-jia-bu-yao-kan-labuladong-de-jie-fa-fei-chang-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 完整代码

```java
public class Solution {

    public int[] searchRange(int[] nums, int target) {
        if (nums.length == 0) {
            return new int[]{-1, -1};
        }
        int firstPosition = findFirstPosition(nums, target);
        if (firstPosition == -1) {
            return new int[]{-1, -1};
        }
        int lastPosition = findLastPosition(nums, target);
        return new int[]{firstPosition, lastPosition};
    }


    private int findFirstPosition(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                // ① 不可以直接返回，应该继续向左边找，即 [left, mid - 1] 区间里找
                right = mid - 1;
            } else if (nums[mid] < target) {
                // 应该继续向右边找，即 [mid + 1, right] 区间里找
                left = mid + 1;
            } else {
                // 此时 nums[mid] > target，应该继续向左边找，即 [left, mid - 1] 区间里找
                right = mid - 1;
            }
        }

        // 此时 left 和 right 的位置关系是 [right, left]，注意上面的 ①，此时 left 才是第 1 次元素出现的位置
        // 因此还需要特别做一次判断
        if (left != nums.length && nums[left] == target) {
            return left;
        }
        return -1;
    }

    private int findLastPosition(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                // 只有这里不一样：不可以直接返回，应该继续向右边找，即 [mid + 1, right] 区间里找
                left = mid + 1;
            } else if (nums[mid] < target) {
                // 应该继续向右边找，即 [mid + 1, right] 区间里找
                left = mid + 1;
            } else {
                // 此时 nums[mid] > target，应该继续向左边找，即 [left, mid - 1] 区间里找
                right = mid - 1;
            }
        }
        // 由于 findFirstPosition 方法可以返回是否找到，这里无需单独再做判断
        return right;
    }
}

作者：yong-cai-gu-zi-xi
链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/da-jia-bu-yao-kan-labuladong-de-jie-fa-fei-chang-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210314014950835](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314014952.png)

`while(left < right)` 写法的好处在于退出循环的时候 `left` 与 `right` 的重合

![image-20210314015322452](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314015322.png)







![image-20210314015520239](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314015520.png)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.length && nums[leftIdx] == target && nums[rightIdx] == target) {
            return new int[]{leftIdx, rightIdx};
        } 
        return new int[]{-1, -1};
    }

    public int binarySearch(int[] nums, int target, boolean lower) {
        int left = 0, right = nums.length - 1, ans = nums.length;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
                ans = mid;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/zai-pai-xu-shu-zu-zhong-cha-zhao-yuan-su-de-di-3-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 43、[搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

![image-20210314021402342](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314021402.png)

### 思路

#### 二分查找理解

![image-20210314021953500](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314021953.png)

#### 二分查找的核心思想

二分查找的核心思想是「减而治之」，即「不断缩小问题规模」。

#### 二分查找的两种思路

（请特别留意第 2 种思路，掌握它能思路清晰地解决「力扣」上的所有二分查找问题）

##### 思路 1：在循环体内部查找元素

##### while(left <= right)

![image-20210314022201440](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314022201.png)

![img](https://pic.leetcode-cn.com/1598340841-leuSyu-file_1598340837977)

##### 思路 2：在循环体内部排除元素（重点）while(left < right)

![img](https://pic.leetcode-cn.com/1598340841-tpXMfu-file_1598340837988)

第 2 种思路可以归纳为「左右边界向中间走，两边夹」，这种思路在解决复杂问题的时候，可以使得思考的过程变得简单。

![image-20210314022819793](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314022820.png)

```java
public class Solution {

    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }

        // 特判
        if (nums[len - 1] < target) {
            return len;
        }
        int left = 0;
        int right = len - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                // 下一轮搜索区间是 [left, mid]
                right = mid;
            }
        }
        return left;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/search-insert-position/solution/te-bie-hao-yong-de-er-fen-cha-fa-fa-mo-ban-python-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

由于插入元素的位置可能在数组的末尾下标的下一个（见例 3），因此在初始化右边界 `right` 的时候，可以设置成为数组的长度 `len`。代码还可以这样写：

```java
public class Solution {

    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
        
        int left = 0;
        // 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
        int right = len;
        while (left < right) {
            int mid = left + (right - left) / 2;
            // 小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索的区间是 [mid + 1, right]
                left = mid + 1;
            } else {
              	// 下一轮搜索的区间是 [left, mid]
                right = mid;
            }
        }
        return left;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/search-insert-position/solution/te-bie-hao-yong-de-er-fen-cha-fa-fa-mo-ban-python-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 44、[搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

![image-20210314175640251](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314175641.png)

### 思路

#### 二分查找

##### 有重复元素的处理

![image-20210314175941469](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314175941.png)

```java
public boolean search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return false;
        }
        int start = 0;
        int end = nums.length - 1;
        int mid;
        while (start <= end) {
            mid = start + (end - start) / 2;
            if (nums[mid] == target) {
                return true;
            }
            if (nums[start] == nums[mid]) {
                start++;
                continue;
            }
            //前半部分有序
            if (nums[start] < nums[mid]) {
                //target在前半部分
                if (nums[mid] > target && nums[start] <= target) {
                    end = mid - 1;
                } else {  //否则，去后半部分找
                    start = mid + 1;
                }
            } else {
                //后半部分有序
                //target在后半部分
                if (nums[mid] < target && nums[end] >= target) {
                    start = mid + 1;
                } else {  //否则，去后半部分找
                    end = mid - 1;

                }
            }
        }
        //一直没找到，返回false
        return false;

    }

作者：reedfan
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/zai-javazhong-ji-bai-liao-100de-yong-hu-by-reedfan/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 45、[寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

![image-20210314181008602](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314181008.png)

### 思路

#### 二分查找

这道寻找最小值的题目可以用二分查找法来解决，时间复杂度为O(logN)，空间复杂度为O(1)。

看一下代码：（关键在于确定最小值在哪个半边）

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {          
                left = mid + 1;
            } else {                                
                right = mid;
            }
        }
        return nums[left];
    }
};

作者：armeria-program
链接：https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/solution/er-fen-cha-zhao-wei-shi-yao-zuo-you-bu-dui-cheng-z/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210314182551431](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314182552.png)

![image-20210314182913145](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314182913.png)

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;                /* 左闭右闭区间，如果用右开区间则不方便判断右值 */ 
        while (left < right) {                      /* 循环不变式，如果left == right，则循环结束 */
            int mid = left + (right - left) / 2;    /* 地板除，mid更靠近left */
            if (nums[mid] > nums[right]) {          /* 中值 > 右值，最小值在右半边，收缩左边界 */ 
                left = mid + 1;                     /* 因为中值 > 右值，中值肯定不是最小值，左边界可以跨过mid */ 
            } else if (nums[mid] < nums[right]) {   /* 明确中值 < 右值，最小值在左半边，收缩右边界 */ 
                right = mid;                        /* 因为中值 < 右值，中值也可能是最小值，右边界只能取到mid处 */ 
            }
        }
        return nums[left];    /* 循环结束，left == right，最小值输出nums[left]或nums[right]均可 */     
    }
};

作者：armeria-program
链接：https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/solution/er-fen-cha-zhao-wei-shi-yao-zuo-you-bu-dui-cheng-z/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210314183417648](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314183418.png)

46、[寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

![image-20210314193443498](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314193444.png

![image-20210314193511264](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314193511.png)

![image-20210314194109144](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314194109.png)

![img](https://pic.leetcode-cn.com/5c4b7d4827e1538ca8f5a67f2c64705304ae985f80bd1e43d6f193bd2f2dea57-Picture1.png)

![image-20210314194339055](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314194339.png)

![image-20210314194409933](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314194410.png)

![image-20210314194434860](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314194435.png)

![image-20210314194443551](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314194443.png)

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] > nums[right]) left = mid + 1;
            else if (nums[mid] < nums[right]) right = mid;
            else right = right - 1;
        }
        return nums[left];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/solution/154-find-minimum-in-rotated-sorted-array-ii-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 46、[最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

![image-20210314223456309](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210314223456.png)

### 思路

#### 动态规划

##### 核心思想

> 动态规划的核心设计思想是数学归纳法。
>
> 相信大家对数学归纳法都不陌生，高中就学过，而且思路很简单。比如我们想证明一个数学结论，那么我们先假设这个结论在 k<n 时成立，然后根据这个假设，想办法推导证明出 k=n 的时候此结论也成立。如果能够证明出来，那么就说明这个结论对于 k 等于任何数都成立。
>
> 类似的，我们设计动态规划算法，不是需要一个 dp 数组吗？我们可以假设 dp[0...i-1] 都已经被算出来了，然后问自己：怎么通过这些结果算出 dp[i]？
>
> 直接拿最长递增子序列这个问题举例你就明白了。不过，首先要定义清楚 dp 数组的含义，即 dp[i] 的值到底代表着什么？
>
> 我们的定义是这样的：**dp[i] 表示以 nums[i] 这个数结尾的最长递增子序列的长度。**
> 
>

![image-20210315032314910](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315032315.png)

算法演进的过程是这样的：

![gif1](https://pic.leetcode-cn.com/484ffdf77a8fb4853cb4b83678e682be6cd7d0979c807a00f422c5c78c56f709.gif)

![image-20210315032631612](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315032632.png)

![gif2](https://pic.leetcode-cn.com/8fe73d010abfdbb0ff4950e96640bb8a04391fc7438fed845ab6d36208468266.gif)

![image-20210315032829786](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315032830.png)

```java
public int lengthOfLIS(int[] nums) {
    int[] dp = new int[nums.length];
    // base case：dp 数组全都初始化为 1
    Arrays.fill(dp, 1);
    for (int i = 0; i < nums.length; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) 
                dp[i] = Math.max(dp[i], dp[j] + 1);
        }
    }
    
    int res = 0;
    for (int i = 0; i < dp.length; i++) {
        res = Math.max(res, dp[i]);
    }
    return res;
}

作者：labuladong
链接：https://leetcode-cn.com/problems/longest-increasing-subsequence/solution/dong-tai-gui-hua-she-ji-fang-fa-zhi-pai-you-xi-jia/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210315034003766](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315034004.png)

#### 二分查找

这个解法的时间复杂度为 O(NlogN)，但是说实话，正常人基本想不到这种解法（也许玩过某些纸牌游戏的人可以想出来）。所以大家了解一下就好，正常情况下能够给出动态规划解法就已经很不错了。

根据题目的意思，我都很难想象这个问题竟然能和二分查找扯上关系。其实最长递增子序列和一种叫做 patience game 的纸牌游戏有关，甚至有一种排序方法就叫做 patience sorting（耐心排序）。

![image-20210315035231291](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315035231.png)

![image-20210315035726486](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315035726.png)

如果是没找到牌堆的话，这个时候left肯定是往最高位跑的，但是又碰不到，这个时候就要新建

一个牌堆了，把牌放到这个牌堆上

注意，这个时候right=piles，即right这个堆可能是没有放东西的，而且这个时候left=right

![image-20210315042801069](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210315042801.png)

## 47、[山脉数组的峰顶索引](https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/)

![image-20210316020929508](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210316020931.png)

### 思路

#### 二分查找

### 模板1：while(left<=right)

- 循环体内有3个分支
- 在循环体中返回目标索引

```java
class Solution {
    public int peakIndexInMountainArray(int[] nums) {
        int left=0;
        int right=nums.length-1;
        while(left<=right){
            int mid=left+(right-left)/2;
            if(nums[mid]>nums[mid+1]&&nums[mid]>nums[mid-1]){
                return mid;
            }
            else if(nums[mid]>nums[mid+1]){
                right=mid-1;
            }
            else {
                left=mid+1;
            }
        }
        // 这里return什么都可以，因为对于此题来说，在循环体内一定会返回
        return -1;

    }
}

作者：carryzz
链接：https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/solution/er-fen-cha-zhao-liang-chong-mo-ban-leftrighthe-lef/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 模板2：while(left<right)

- 循环体内有2个分支
- 在循环体外返回目标索引，在循环体内缩减搜索区间

```java
class Solution {
    public int peakIndexInMountainArray(int[] nums) {
        int left=0;
        int right=nums.length-1;
        while(left<right){
            int mid=left+(right-left)/2;
            // 缩减区间为[mid+1,right]
            if(nums[mid]<nums[mid+1]){
                left=mid+1;
            }
            // 缩减区间为[left,mid]
            else {
                right=mid;
            }
        }
        // left=right时退出循环，返回left或right是一样的
        return left;

    }
}

作者：carryzz
链接：https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/solution/er-fen-cha-zhao-liang-chong-mo-ban-leftrighthe-lef/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



48、[山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/)

![image-20210317025127985](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210317025128.png)

### 思路

#### 二分查找

![image-20210317030252544](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210317030253.png)

![image-20210317032908714](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210317032909.png)

```java
class Solution {
    int binary_search(MountainArray &mountain, int target, int l, int r, int key(int)) {
        target = key(target);
        while (l <= r) {
            int mid = (l + r) / 2;
            int cur = key(mountain.get(mid));
            if (cur == target) {
                return mid;
            } else if (cur < target) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return -1;
    }
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int l = 0, r = mountainArr.length() - 1;
        while (l < r) {
            int mid = (l + r) / 2;
            if (mountainArr.get(mid) < mountainArr.get(mid + 1)) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        
        int peak = l;
        int index = binary_search(mountainArr, target, 0, peak, [](int x) -> int{return x;});
        if (index != -1) {
            return index;
        }
        return binary_search(mountainArr, target, peak + 1, mountainArr.length() - 1, [](int x) -> int{return -x;});
    }
};

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/find-in-mountain-array/solution/shan-mai-shu-zu-zhong-cha-zhao-mu-biao-zhi-by-leet/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210317033116968](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210317033117.png)

##### 复杂度分析

## 48、[转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/)

![image-20210319053102772](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319053104.png)

### 思路

#### 二分查找

![image-20210319053338245](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319053338.png)

![image-20210319053641267](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319053641.png)

![image-20210319053931501](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319053931.png)

```java
public class Solution {

    public int findBestValue(int[] arr, int target) {
        int left = 0;
        int right = 0;
        // 注意：
        for (int num : arr) {
            right = Math.max(right, num);
        }

        while (left < right) {
            int mid = left + (right - left) / 2;
            int sum = calculateSum(arr, mid);
            // 计算第 1 个使得转变后数组的和大于等于 target 的阈值 threshold
            if (sum < target) {
                // 严格小于的一定不是解
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        // 比较阈值线分别定在 left - 1 和 left 的时候与 target 的接近程度
        int sum1 = calculateSum(arr, left - 1);
        int sum2 = calculateSum(arr, left);
        if (target - sum1 <= sum2 - target) {
            return left - 1;
        }
        return left;
    }

    private int calculateSum(int[] arr, int threshold) {
        int sum = 0;
        for (int num : arr) {
            sum += Math.min(num, threshold);
        }
        return sum;
    }

    public static void main(String[] args) {
        int[] arr = new int[]{2, 3, 5};
        int target = 11;
        Solution solution = new Solution();
        int res = solution.findBestValue(arr, target);
        System.out.println(res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/solution/er-fen-cha-zhao-by-liweiwei1419-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210319054453150](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319054453.png)

![image-20210319055718314](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319055718.png)

如果选择一个阈值 value ，使得它对应的 sum 是最后 1 个小于等于 target 的阈值，那么目标值可能在 value 也可能在 value + 1。

![image-20210319060416861](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319060417.png)

```java
public class Solution {

    public int findBestValue(int[] arr, int target) {
        int left = 0;
        int right = 0;
        // 注意：
        for (int num : arr) {
            right = Math.max(right, num);
        }

        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            int sum = calculateSum(arr, mid);
            // 计算最后 1 个使得转变以后数组的和小于等于 target 的阈值 threshold
            if (sum > target) {
                // 大于等于的就不是解，threshold 太大了，下一轮搜索区间是 [left, mid - 1]
                right = mid - 1;
            } else {
                // 下一轮搜索区间是 [mid, right]
                left = mid;
            }
        }

        // 比较阈值线分别定在 left 和 left + 1 的时候与 target 的接近程度
        int sum1 = calculateSum(arr, left);
        int sum2 = calculateSum(arr, left + 1);
        // 注意：这里必须加绝对值，因为有可能出现 sum1 == sum2 < target 的情况
        if (Math.abs(target - sum1) <= Math.abs(sum2 - target)) {
            return left;
        }
        return left + 1;
    }

    private int calculateSum(int[] arr, int threshold) {
        int sum = 0;
        for (int num : arr) {
            sum += Math.min(num, threshold);
        }
        return sum;
    }

    public static void main(String[] args) {
        int[] arr = new int[]{2, 3, 5};
        int target = 11;
        Solution solution = new Solution();
        int res = solution.findBestValue(arr, target);
        System.out.println(res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/solution/er-fen-cha-zhao-by-liweiwei1419-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 复杂度分析

![image-20210319060446730](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210319060446.png)

## 49、[ x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

### 思路

#### 二分法

![image-20210320033102410](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320033105.png)

![image-20210320033207998](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320033208.png)

![image-20210320040111700](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320040112.png)

## 50、[寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

![image-20210320053251866](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320053253.png)

### 思路

#### 二分法

##### 抽屉原理

![image-20210320054403577](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320054404.png)

![image-20210320054533031](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320054533.png)

![image-20210320054629599](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320054629.png)

```java
public class Solution {

    public int findDuplicate(int[] nums) {
        int len = nums.length;
        int left = 1;
        int right = len - 1;
        while (left < right) {
            // 在 Java 里可以这么用，当 left + right 溢出的时候，无符号右移保证结果依然正确
            int mid = (left + right) >>> 1;
            
            int cnt = 0;
            for (int num : nums) {
                if (num <= mid) {
                    cnt += 1;
                }
            }

            // 根据抽屉原理，小于等于 4 的个数如果严格大于 4 个
            // 此时重复元素一定出现在 [1, 4] 区间里
            if (cnt > mid) {
                // 重复元素位于区间 [left, mid]
                right = mid;
            } else {
                // if 分析正确了以后，else 搜索的区间就是 if 的反面
                // [mid + 1, right]
                left = mid + 1;
            }
        }
        return left;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/find-the-duplicate-number/solution/er-fen-fa-si-lu-ji-dai-ma-python-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 复杂度分析

![image-20210320054711171](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320054711.png)

![image-20210320054843488](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320054843.png)

#### 快慢指针

![image-20210320152729398](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320152730.png)

![image-20210320170823512](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320170824.png)

![image-20210320171044670](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320171045.png)

![image-20210320171406702](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320171407.png)

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = 0;
        int fast = 0;
        slow = nums[slow];
        fast = nums[nums[fast]];
        while(slow != fast){
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        int pre1 = 0;
        int pre2 = slow;
        while(pre1 != pre2){
            pre1 = nums[pre1];
            pre2 = nums[pre2];
        }
        return pre1;
    }
}

作者：kirsche
链接：https://leetcode-cn.com/problems/find-the-duplicate-number/solution/287xun-zhao-zhong-fu-shu-by-kirsche/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210320171629223](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320171629.png)

![image-20210320172409638](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320172409.png)

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = 0, fast = 0;
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        slow = 0;
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/find-the-duplicate-number/solution/xun-zhao-zhong-fu-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### **复杂度分析**

![image-20210320172439882](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320172440.png)

#### 排序

#### ![image-20210320172519756](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320172520.png)HashSet

![image-20210320172540947](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320172541.png)

## 51、[缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

![image-20210320213119601](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210320213120.png)

### 思路

#### （1）哈希表

![image-20210321004409468](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321004409.png)

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            if (nums[i] <= 0) {
                nums[i] = n + 1;
            }
        }
        for (int i = 0; i < n; ++i) {
            int num = Math.abs(nums[i]);
            if (num <= n) {
                nums[num - 1] = -Math.abs(nums[num - 1]);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] > 0) {
                return i + 1;
            }
        }
        return n + 1;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/first-missing-positive/solution/que-shi-de-di-yi-ge-zheng-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210321010036395](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321010036.png)

###### 复杂度分析

![image-20210321004449701](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321004449.png)

虽然有三个for循环，但是是固定的3N次，可以把常数去掉

这里没有使用hash表，而是用**数组的索引**来代替，减少空间的使用

长度为N的无序数组在保证“无重复，无越界”的情况下，最多能装下N以内的正整数。

#### （2）置换

![image-20210321010859174](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321010859.png)

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/first-missing-positive/solution/que-shi-de-di-yi-ge-zheng-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```





![image-20210321011725311](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321011725.png)

![image-20210321011753181](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321011753.png)

#### （3）双指针

![image-20210321020116814](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321020117.png)

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int res = 0;
        int n = nums.length - 1;
        int tmp = 0;

        while(res <= n){
            if(nums[res] == res + 1){
                res++;
            }else{
                tmp = nums[res];
                if(tmp > n + 1 || tmp < res + 1 || nums[tmp - 1] == tmp){
                    nums[res] = nums[n--];
                }else{
                    nums[res] = nums[tmp - 1];
                    nums[tmp - 1] = tmp;
                }
            }
        }

        return res + 1;
    }
}

作者：walkerwk
链接：https://leetcode-cn.com/problems/first-missing-positive/solution/javashuang-zhi-zhen-shi-jian-fu-za-du-on-kong-jian/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 52、[剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

![image-20210321024849299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321024849.png)

#### （1）原地置换

![image-20210321024936893](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321024937.png)

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        int temp;
        for(int i=0;i<nums.length;i++){
            while (nums[i]!=i){
                if(nums[i]==nums[nums[i]]){
                    return nums[i];
                }
                temp=nums[i];
                nums[i]=nums[temp];
                nums[temp]=temp;
            }
        }
        return -1;
    }
}

作者：derrick_sun
链接：https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/solution/yuan-di-zhi-huan-shi-jian-kong-jian-100-by-derrick/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）哈希表/Set

![image-20210321025213599](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321025213.png)

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Set<Integer> dic = new HashSet<>();
        for(int num : nums) {
            if(dic.contains(num)) return num;
            dic.add(num);
        }
        return -1;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/solution/mian-shi-ti-03-shu-zu-zhong-zhong-fu-de-shu-zi-yua/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）排序

排序以后，再遍历一遍就知道哪个重复了。

分析：这个方法其实比较容易想到，但是时间复杂度是 O(N \log N)*O*(*N*log*N*)，同时也修改了原始数组。

## 53、[剑指 Offer  0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

![image-20210321062154067](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321062154.png)

### 思路

#### （1）二分查找法

![image-20210321062621219](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321062621.png)

![img](https://pic.leetcode-cn.com/515ad046b4363de9324acc0ce74d9fa9048d5526c66a91539b99c26fbe4a49cb-Picture2.png)

![img](https://pic.leetcode-cn.com/f453ae298ff6d085358376ab32ea59932a1a1b8f93d56de9eb0ccb74fb65c8a1-Picture3.png)

![img](https://pic.leetcode-cn.com/02ca857fbbc95a3d73a5bbe717e578c03357130b8e1fd92c409d0ca5c9d099c0-Picture4.png)

![img](https://pic.leetcode-cn.com/5c7feffb94f79db42836688635309d34b7576b519eb84337ab5977cf480ea0ff-Picture5.png)

![img](https://pic.leetcode-cn.com/b57e2d7cfd5f23ef22681bfe5fa59882c143097b0e28631dfaafcf7000027484-Picture6.png)

![img](https://pic.leetcode-cn.com/1cdbdb450af28f6db2aa8a1c8530a7d96b8293779392ee29efb47b025317851e-Picture7.png)

###### 复杂度分析

![image-20210321062725378](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321062725.png)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] == m) i = m + 1;
            else j = m - 1;
        }
        return i;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/solution/mian-shi-ti-53-ii-0n-1zhong-que-shi-de-shu-zi-er-f/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 链表

## 54、[两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

![image-20210321064534030](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321064534.png)

### 思路

#### 预先指针

![image-20210321065134365](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321065134.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode pre = new ListNode(0);
        ListNode cur = pre;
        int carry = 0;
        while(l1 != null || l2 != null) {
            int x = l1 == null ? 0 : l1.val;
            int y = l2 == null ? 0 : l2.val;
            int sum = x + y + carry;
            
            carry = sum / 10;
            sum = sum % 10;
            cur.next = new ListNode(sum);

            cur = cur.next;
            if(l1 != null)
                l1 = l1.next;
            if(l2 != null)
                l2 = l2.next;
        }
        if(carry == 1) {
            cur.next = new ListNode(carry);
        }
        return pre.next;
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/add-two-numbers/solution/hua-jie-suan-fa-2-liang-shu-xiang-jia-by-guanpengc/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![img](https://pic.leetcode-cn.com/2519bd7f7da0f3bd51dd0f06e6363f4f62bfb25472c5ec233cf969e5c1472e33-file_1559748028103)

![img](https://pic.leetcode-cn.com/400f2a615319c4f0f42c39eb8b8902984922d1e778ca461569ff64460eaa9757-file_1559748028117)

![img](https://pic.leetcode-cn.com/e0d3266ec83cee00c6a0ff0a8a66de8d129798b24b76a19b7883f2fd1d79c15b-file_1559748087173)

![img](https://pic.leetcode-cn.com/a5bf6bc2cc15d162bd35eb8fc467fb36887e40b36c26bdc982a11a686b34cb30-file_1559748028113)

![img](https://pic.leetcode-cn.com/fc6475aca0ec0621003f4888a59086c398ff5fc6ee2e27cbfb9bc91f107383b9-file_1559748028094)

![img](https://pic.leetcode-cn.com/743afc3cb34954e1f3a9b41924d4af5453832d23772a2e46aa4cd52a2b240bdd-file_1559748028108)

![img](https://pic.leetcode-cn.com/3323b948431675b9f2ff8b0161eee9178298cbb4403cbcd36dc857f14043cf7a-file_1559748028112)

![img](https://pic.leetcode-cn.com/508d1bb12a372e385c4052d95ca92e06c3a63a805bf12feddd0bb4e7c972f016-file_1559748028116)

## 55、[445. 两数相加 II](https://leetcode-cn.com/problems/add-two-numbers-ii/)

![image-20210321163625253](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321163625.png)

### 思路

#### 头插法+栈

![image-20210321170554585](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321170555.png)

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Deque<Integer> stack1 = new LinkedList<Integer>();
        Deque<Integer> stack2 = new LinkedList<Integer>();
        while (l1 != null) {
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            stack2.push(l2.val);
            l2 = l2.next;
        }
        int carry = 0;
        ListNode ans = null;
        while (!stack1.isEmpty() || !stack2.isEmpty() || carry != 0) {
            int a = stack1.isEmpty() ? 0 : stack1.pop();
            int b = stack2.isEmpty() ? 0 : stack2.pop();
            int cur = a + b + carry;
            carry = cur / 10;
            cur %= 10;
            ListNode curnode = new ListNode(cur);
            curnode.next = ans;
            ans = curnode;
        }
        return ans;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/add-two-numbers-ii/solution/liang-shu-xiang-jia-ii-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 56、[两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

![image-20210321171824776](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321171825.png)

### 思路

#### 递归

写递归，不要考虑递归是如何在计算机中运行的。考虑的是递归出口和递归关系

递归重要的是递归公式和出口，只要两者对，就一定能递归出结果。如果所有递归都按你画的图这样去思考，你写递归能累死自己的。

![image-20210321174219011](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321174219.png)

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode next = head.next;
        head.next = swapPairs(next.next);
        next.next = head;
        return next;
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/swap-nodes-in-pairs/solution/hua-jie-suan-fa-24-liang-liang-jiao-huan-lian-biao/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210321190119018](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321190119.png)

#### 非递归

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode pre = new ListNode(0);
        pre.next = head;
        ListNode temp = pre;
        while(temp.next != null && temp.next.next != null) {
            ListNode start = temp.next;
            ListNode end = temp.next.next;
            temp.next = end;
            start.next = end.next;
            end.next = start;
            temp = start;
        }
        return pre.next;
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/swap-nodes-in-pairs/solution/hua-jie-suan-fa-24-liang-liang-jiao-huan-lian-biao/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210321190515930](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321190516.png)

## 57、[ 旋转链表](https://leetcode-cn.com/problems/rotate-list/)

![image-20210321191811410](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321191811.png)

### 思路

#### 双指针

![image-20210321230928361](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321230928.png)

![image-20210321231129725](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321231130.png)

```java
public ListNode rotateRight(ListNode head, int k) {
    if (head == null)
        return head;
    ListNode fast = head, slow = head;
    //链表的长度
    int len = 1;
    //统计链表的长度，顺便找到链表的尾结点
    while (fast.next != null) {
        len++;
        fast = fast.next;
    }
    //首尾相连，先构成环
    fast.next = head;
    //慢指针移动的步数
    int step = len - k % len;
    //移动步数，这里大于1实际上是少移了一步
    while (step-- > 1) {
        slow = slow.next;
    }
    //temp就是需要返回的结点
    ListNode temp = slow.next;
    //因为链表是环形的，slow就相当于尾结点了，
    //直接让他的next等于空
    slow.next = null;
    return temp;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/rotate-list/solution/61-xuan-zhuan-lian-biao-tu-wen-xiang-jie-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210321231720209](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321231720.png)

![img](https://pic.leetcode-cn.com/1599919040-ItvCAU-image.png)

![img](https://pic.leetcode-cn.com/1599919110-EKcRVV-image.png)

![img](https://pic.leetcode-cn.com/1599919252-jTpdae-image.png)

![img](https://pic.leetcode-cn.com/1599919282-tYgOxT-image.png)

![img](https://pic.leetcode-cn.com/1599919314-dFsnRb-image.png)

![img](https://pic.leetcode-cn.com/1599919345-qIwTqd-image.png)

![img](https://pic.leetcode-cn.com/1599919593-gowVjn-image.png)

![img](https://pic.leetcode-cn.com/1599919644-lxKeLu-image.png)

![img](https://pic.leetcode-cn.com/1599919983-NCwznu-image.png)

![img](https://pic.leetcode-cn.com/1599920066-cNlBFz-image.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if(head==null||head.next==null||k==0){return head;}
        ListNode tmp = head;
        int count = 0;
        while(tmp!=null){
            tmp = tmp.next;
            count += 1;
        }
        k = k % count;
        if(k==0) return head;
        ListNode p = head, fast = head, slow = head;
        for(int i = 0; i < k; i++){
            fast = fast.next;
        }
        while(fast.next != null){
            fast = fast.next;
            slow = slow.next;
        }
        ListNode newHead = slow.next;
        slow.next = null;
        fast.next = head;
        return newHead;
    }
}

作者：LeahChao
链接：https://leetcode-cn.com/problems/rotate-list/solution/kuai-man-zhi-zhen-xi-jie-yao-gao-hao-by-lilychao/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### 不用双指针

得到链表长度之后，直接计算头尾的位置

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if(head==null||head.next==null||k==0){return head;}
        ListNode tmp = head;
        int count = 1;
        while(tmp.next!=null){
            tmp = tmp.next;
            count += 1;
        }
        k = k % count;
        if(k==0) return head;
        ListNode newHead = head, newTail = head;
        for(int i = 0; i < count - k; i++){
            newHead = newHead.next;
        }
        //这里不用遍历其实，得到尾节点，它的下个结点就是newHead
         //for (let i = 0; i < count - k - 1; i ++) {
      	//	newTail = newTail.next
  		//}
    	//newHead = newTail.next
        for(int j = 0; j < count - k - 1; j++){
            newTail = newTail.next;
        }
        tmp.next = head;
        newTail.next = null;
        return newHead;
    }
}

作者：LeahChao
链接：https://leetcode-cn.com/problems/rotate-list/solution/kuai-man-zhi-zhen-xi-jie-yao-gao-hao-by-lilychao/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210321232621575](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321232621.png)

## 58、[剑指 Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

![image-20210321235854081](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210321235854.png)

### 思路

#### 双指针

![image-20210322000215256](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210322000215.png)

<img src="https://pic.leetcode-cn.com/f82d0c4ade03ae2107d3cc5b29480e0f0d48d47822dcf5776ea0e8fde41efe03-Picture1.png" alt="img" style="zoom:67%;" />

<img src="https://pic.leetcode-cn.com/399741478b69166a5b801ab2b0f72dbebe3fd5d612a0328f2ca4ea5c4043f2a9-Picture2.png" alt="img" style="zoom:67%;" />

<img src="https://pic.leetcode-cn.com/71a5d105a41e7220db9948f377e7dae6b046c578a2fc73b14cbca47092d72e32-Picture3.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/d14cd267e7a0fe71efbb6106f4ccb1fcc3c68faf30c3ce3ee87b14371781436f-Picture4.png)

![img](https://pic.leetcode-cn.com/d14cd267e7a0fe71efbb6106f4ccb1fcc3c68faf30c3ce3ee87b14371781436f-Picture4.png)

<img src="https://pic.leetcode-cn.com/468908ded106c3e71eb868bd864bcf733f9595643e6259ec5db2efd2fed1e58e-Picture5.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/951650517cba6954a0480240bcb8c1d9b12c3bba568c07b267fecaaf49946e02-Picture6.png)

<img src="https://pic.leetcode-cn.com/468908ded106c3e71eb868bd864bcf733f9595643e6259ec5db2efd2fed1e58e-Picture5.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/951650517cba6954a0480240bcb8c1d9b12c3bba568c07b267fecaaf49946e02-Picture6.png)![img](https://pic.leetcode-cn.com/924c58447a25fdfa664dba9649d83e2e0b41a7136238696bfb24a363cbc68bb2-Picture7.png)

<img src="https://pic.leetcode-cn.com/468908ded106c3e71eb868bd864bcf733f9595643e6259ec5db2efd2fed1e58e-Picture5.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/951650517cba6954a0480240bcb8c1d9b12c3bba568c07b267fecaaf49946e02-Picture6.png)![img](https://pic.leetcode-cn.com/924c58447a25fdfa664dba9649d83e2e0b41a7136238696bfb24a363cbc68bb2-Picture7.png)![img](https://pic.leetcode-cn.com/b9ae9b30d44936a55058bda21fe1102a56336ac8947c7620e007b66090ba30f1-Picture8.png)

<img src="https://pic.leetcode-cn.com/468908ded106c3e71eb868bd864bcf733f9595643e6259ec5db2efd2fed1e58e-Picture5.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/951650517cba6954a0480240bcb8c1d9b12c3bba568c07b267fecaaf49946e02-Picture6.png)![img](https://pic.leetcode-cn.com/924c58447a25fdfa664dba9649d83e2e0b41a7136238696bfb24a363cbc68bb2-Picture7.png)![img](https://pic.leetcode-cn.com/b9ae9b30d44936a55058bda21fe1102a56336ac8947c7620e007b66090ba30f1-Picture8.png)

##### 复杂度分析

![image-20210322000359098](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210322000359.png)

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode former = head, latter = head;
        for(int i = 0; i < k; i++)
            former = former.next;
        while(former != null) {
            former = former.next;
            latter = latter.next;
        }
        return latter;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/solution/mian-shi-ti-22-lian-biao-zhong-dao-shu-di-kge-j-11/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 59、[面试题 02.03. 删除中间节点](https://leetcode-cn.com/problems/delete-middle-node-lcci/)

![image-20210322032109159](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210322032109.png)

### 思路

![image-20210322032241163](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210322032241.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
//已知一个结点，以及该节点后的所有结点
//求该结点的前一个结点,无法求
//但还知道这个结点的地址，
//可以更换该这个结点的数据为下一个结点，然后删除下一个结点

class Solution {
    public void deleteNode(ListNode node) {
        
        //将这个结点的数据替换为下一个结点
        node.val = node.next.val;
        //删除下一个结点
        node.next = node.next.next;
        
    }
}

作者：zeng-gui-liu-xiang
链接：https://leetcode-cn.com/problems/delete-middle-node-lcci/solution/cai-niao-jie-fa-jin-gong-can-kao-by-zeng-gui-liu-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

这题主要是不能访问头结点，只能从当前的结点往下查询

## 60、[剑指 Offer 18. 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

![image-20210323025158351](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323025159.png)

### 思路

#### （1）双指针

![image-20210323025714590](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323025714.png)

![image-20210323025916617](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323025916.png)

```java
public ListNode deleteNode(ListNode head, int val) {
    //初始化一个虚拟节点
    ListNode dummy = new ListNode(0);
    //让虚拟节点指向头结点
    dummy.next = head;
    ListNode cur = head;
    ListNode pre = dummy;
    while (cur != null) {
        if (cur.val == val) {
            //如果找到要删除的结点，直接把他删除
            pre.next = cur.next;
            break;
        }
        //如果没找到，pre指针和cur指针都同时往后移一步
        pre = cur;
        cur = cur.next;
    }
    //最后返回虚拟节点的下一个结点即可
    return dummy.next;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/solution/shuang-zhi-zhen-he-di-gui-liang-chong-fang-shi-jie/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）不用双指针

![image-20210323030746703](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323030747.png)

```java
public ListNode deleteNode(ListNode head, int val) {
    //边界条件判断
    if (head == null)
        return head;
    //如果要删除的是头结点，直接返回头结点的下一个结点即可
    if (head.val == val)
        return head.next;
    ListNode cur = head;
    //找到要删除结点的上一个结点
    while (cur.next != null && cur.next.val != val) {
        cur = cur.next;
    }
    //删除结点
    cur.next = cur.next.next;
    return head;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/solution/shuang-zhi-zhen-he-di-gui-liang-chong-fang-shi-jie/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）递归

![image-20210323031452996](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323031453.png)

```java
public ListNode deleteNode(ListNode head, int val) {
    if (head == null)
        return head;
    if (head.val == val)
        return head.next;
    head.next = deleteNode(head.next, val);
    return head;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/solution/shuang-zhi-zhen-he-di-gui-liang-chong-fang-shi-jie/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 61、[面试题 02.08. 环路检测](https://leetcode-cn.com/problems/linked-list-cycle-lcci/)

![image-20210323034129222](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323034129.png)

### 思路

#### （1）快慢指针

![image-20210323034853925](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323034854.png)

![image-20210323035514583](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323035514.png)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        while (fast != null) {
            slow = slow.next;
            if (fast.next != null) {
                fast = fast.next.next;
            } else {
                return null;
            }
            if (fast == slow) {
                ListNode ptr = head;
                while (ptr != slow) {
                    ptr = ptr.next;
                    slow = slow.next;
                }
                return ptr;
            }
        }
        return null;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/linked-list-cycle-lcci/solution/huan-lu-jian-ce-by-leetcode-solution-s2la/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210323035554884](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210323035555.png)

#### （2）哈希表

![image-20210324004351459](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210324004352.png)

###### 复杂度分析

![image-20210324004415029](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210324004415.png)



# 哈希表



## 62、[有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

![image-20210324010814721](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210324010815.png)

### 思路

#### 哈希表

![image-20210324010912718](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210324010913.png)

![image-20210325024752367](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210325024752.png)

![image-20210325020628818](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210325020629.png)

```java
   public boolean isValidSudoku(char[][] board) {
        // 记录某行，某位数字是否已经被摆放
        boolean[][] row = new boolean[9][9];
        // 记录某列，某位数字是否已经被摆放
        boolean[][] col = new boolean[9][9];
        // 记录某 3x3 宫格内，某位数字是否已经被摆放
        boolean[][] block = new boolean[9][9];
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] != '.') {
                    int num = board[i][j] - '1';
                    int blockIndex = i / 3 * 3 + j / 3;
                    if (row[i][num] || col[j][num] || block[blockIndex][num]) {
                        return false;
                    } else {
                        row[i][num] = true;
                        col[j][num] = true;
                        block[blockIndex][num] = true;
                    }
                }
            }
        }
        return true;
    }

作者：StackOverflow-
链接：https://leetcode-cn.com/problems/valid-sudoku/solution/java-yi-dong-yi-jie-xiao-lu-gao-by-spirit-9-37/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

> 1. 数字从char直接转换成int，会变成对应的ASCII数字码，而不是原来的数值。解决方法就是当前char数字减'0'：用两个char数字的ASCII码相减，差值就是原来char数值直接对应的int数值。
> 2. 为什么不是减'0'，而是减'1'？ 若运行了大佬的代码你会发现，减'0'的话会出现ArrayIndexOutOfBoundsException的异常。因为后面的代码将这个num作为了数组的下标。本身数组设置的就是9个位置，下标范围是[0~8]，那么遇到数字9作为下标的时候，不就越位了吗，所以就要减1，char转换成int的同时还能解决后面越位的情况。

> 下面这个图是每一个3 * 3格子所在的blockIndex
>
> 0 1 2
>
> 3 4 5
>
> 6 7 8
>
> 先看列，是按照加一的方式增加，所以是j / 3
>
> 再看行，是按照0\*3  1\*3 2*3 的方式增加 所以先除3算出前面的数，也就是0 1 2 ，再乘3
>
> 行 + 列 就是方格中元素的blockIndex



## 63、[最大矩形](https://leetcode-cn.com/problems/maximal-rectangle/)

![image-20210325030636313](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210325030636.png)

![image-20210517074118460](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517074119.png)

### 思路

#### （1）暴力破解

![image-20210518004911715](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518004913.png)

![image-20210518004944299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518004945.png)

###### 代码

```java
public int maximalRectangle(char[][] matrix) {
    if (matrix.length == 0) {
        return 0;
    }
    //保存以当前数字结尾的连续 1 的个数
    int[][] width = new int[matrix.length][matrix[0].length];
    int maxArea = 0;
    //遍历每一行
    for (int row = 0; row < matrix.length; row++) {
        for (int col = 0; col < matrix[0].length; col++) {
            //更新 width
            if (matrix[row][col] == '1') {
                //这个是初始化，第一列，如果当前值是1，那连续个数肯定是1了
                if (col == 0) {
                    width[row][col] = 1;
                } else {
                    width[row][col] = width[row][col - 1] + 1;
                }
            } else {
                width[row][col] = 0;
            }
            //记录所有行中最小的数，初始化为当前的位置的值，即为当前位置连续1的个数
            int minWidth = width[row][col];
            //向上扩展行
            for (int up_row = row; up_row >= 0; up_row--) {
                int height = row - up_row + 1;
                //找最小的数作为矩阵的宽
                minWidth = Math.min(minWidth, width[up_row][col]);
                //更新面积
                maxArea = Math.max(maxArea, height * minWidth);
            }
        }
    }
    return maxArea;
}

作者：windliang
链接：https://leetcode-cn.com/problems/maximal-rectangle/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-1-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210518005926579](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518005927.png)

![image-20210518005947498](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518005948.png)

#### （2）单调栈

![image-20210518012116633](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518012116.png)



![image-20210518012347827](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518012348.png)

###### 代码

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        int row = matrix.length;
        int col = matrix[0].length;
        //这里是首尾各放一个0，便于栈操作
        int[] height = new int[col + 2];
        int res = 0;
        for (int i = 0; i < row; i++) {
            //这里是每层都要开始算一个面积最大值，所以每层开始都开一个栈
            Deque<Integer> stack = new ArrayDeque<>();
            //注意这里的j是能到col+1位置的，因为j是height数组的索引，从0 ~ col+1的
            for (int j = 0; j < col + 2; j++) {
                //这里是表示matrix的j-1的位置的元素放到height的j位置，因为height的0位放的是0，
                //所以从1位开始放，一直放到col位，是可以等于col为的，最后的col+1位还是放0
                if (j >= 1 && j <= col) {
                    //这里height[j]+=1就是上层的连续1的长度+1
                    if (matrix[i][j-1] == '1') height[j] += 1;
                    else height[j] = 0;
                }
                //找到右边界了，右边第一个小于栈顶的元素
                while (!stack.isEmpty() && height[stack.peek()] > height[j]) {
                    int cur = stack.pop();
                    res = Math.max(res, (j - stack.peek() - 1) * height[cur]);
                }
                stack.push(j);
            }
        }
        return res;  
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/maximal-rectangle/solution/yu-zhao-zui-da-ju-xing-na-ti-yi-yang-by-powcai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210518015115316](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518015116.png)

![image-20210518015303729](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518015304.png)

#### （3）动态规划

![image-20210518021235392](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518021235.png)

![image-20210518021245135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518021246.png)



```java
public int maximalRectangle4(char[][] matrix) {
    if (matrix.length == 0) {
        return 0;
    }
    int maxArea = 0;
    int cols = matrix[0].length;
    //左右两边的数组要定义在这里，不要定义在循环里面
    int[] leftLessMin = new int[cols];
    int[] rightLessMin = new int[cols];
    //这些切记要填值初始化
    Arrays.fill(leftLessMin, -1); //初始化为 -1，也就是最左边
    Arrays.fill(rightLessMin, cols); //初始化为 cols，也就是最右边
    int[] heights = new int[cols];
    for (int row = 0; row < matrix.length; row++) {
        //更新所有高度
        for (int col = 0; col < cols; col++) {
            if (matrix[row][col] == '1') {
                heights[col] += 1;
            } else {
                heights[col] = 0;
            }
        }
		//更新所有leftLessMin
        int boundary = -1; //记录本层循环上次出现 0 的位置，0肯定是最小的，而且还要找最近的一个
        //小于当前的高度，那就是记录最近的那个0
        for (int col = 0; col < cols; col++) {
            if (matrix[row][col] == '1') {
                //和上次出现 0 的位置比较，leftLessMin[col]这个是因为可能左边界不变，如果中间
                //没有出现0的话
                leftLessMin[col] = Math.max(leftLessMin[col], boundary);
            } else {
                //当前是 0 代表当前高度是 0，所以初始化为 -1，防止对下次循环的影响
                //这里的下次循环是指下个层的循环，因为如果下层col位置为1，那是要用到上层的                      //leftLessMin[col]的值的，即当前位置的最近的小于当前高度的最左边的索引
                //
                leftLessMin[col] = -1; 
                //更新 0 的位置
                boundary = col;
            }
        }
        //右边同理
        boundary = cols;
        for (int col = cols - 1; col >= 0; col--) {
            if (matrix[row][col] == '1') {
                rightLessMin[col] = Math.min(rightLessMin[col], boundary);
            } else {
                rightLessMin[col] = cols;
                boundary = col;
            }
        }
		
        //更新所有面积，用col可以从左右数组中取出这个col对应的左右边界，相减得到宽度
        for (int col = cols - 1; col >= 0; col--) {
            int area = (rightLessMin[col] - leftLessMin[col] - 1) * heights[col];
            maxArea = Math.max(area, maxArea);
        }

    }
    return maxArea;

}


作者：windliang
链接：https://leetcode-cn.com/problems/maximal-rectangle/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-1-8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210518021400193](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518021401.png)

![image-20210518031507054](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518031508.png)

## 64、[字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

![image-20210326035626756](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326035627.png)

### 思路

#### （1）排序后哈希

![image-20210326040932013](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326040932.png)

```java
public List<List<String>> groupAnagrams(String[] strs) {
    HashMap<String, List<String>> hash = new HashMap<>();
    for (int i = 0; i < strs.length; i++) {
        char[] s_arr = strs[i].toCharArray();
        //排序
        Arrays.sort(s_arr);
        //映射到 key
        String key = String.valueOf(s_arr); 
        //添加到对应的类中
        if (hash.containsKey(key)) {
            hash.get(key).add(strs[i]);
        } else {
            List<String> temp = new ArrayList<String>();
            temp.add(strs[i]);
            hash.put(key, temp);
        }

    }
    return new ArrayList<List<String>>(hash.values()); 
}

作者：windliang
链接：https://leetcode-cn.com/problems/group-anagrams/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--16/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210326041017170](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326041017.png)

###### 复杂度分析

![image-20210326044635845](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326044636.png)

#### （2）计数哈希

![image-20210326045414839](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326045415.png)

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<String, List<String>>();
        for (String str : strs) {
            int[] counts = new int[26];
            int length = str.length();
            for (int i = 0; i < length; i++) {
                counts[str.charAt(i) - 'a']++;
            }
            // 将每个出现次数大于 0 的字母和出现次数按顺序拼接成字符串，作为哈希表的键
            StringBuffer sb = new StringBuffer();
            for (int i = 0; i < 26; i++) {
                if (counts[i] != 0) {
                    sb.append((char) ('a' + i));
                    sb.append(counts[i]);
                }
            }
            String key = sb.toString();
            List<String> list = map.getOrDefault(key, new ArrayList<String>());
            list.add(str);
            map.put(key, list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/group-anagrams/solution/zi-mu-yi-wei-ci-fen-zu-by-leetcode-solut-gyoc/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210326045440907](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326045441.png)

> 那么我们就 把这个数组手动编码变成字符串就行了。
> 比如将 [b,a,a,a,b,c] 编码成 a3b2c1，使用编码后的字符串作为 HashMap 的 Key 进行聚合。

#### （3）质数哈希

![image-20210326050017190](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326050017.png)

```java
public List<List<String>> groupAnagrams(String[] strs) {
    HashMap<Integer, List<String>> hash = new HashMap<>();
    //每个字母对应一个质数
    int[] prime = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101, 103 };
    for (int i = 0; i < strs.length; i++) {
        int key = 1;
        //累乘得到 key
        for (int j = 0; j < strs[i].length(); j++) {
            key *= prime[strs[i].charAt(j) - 'a'];
        } 
        if (hash.containsKey(key)) {
            hash.get(key).add(strs[i]);
        } else {
            List<String> temp = new ArrayList<String>();
            temp.add(strs[i]);
            hash.put(key, temp);
        }

    }
    return new ArrayList<List<String>>(hash.values());
}


作者：windliang
链接：https://leetcode-cn.com/problems/group-anagrams/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--16/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210326050119577](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326050119.png)

## 65、[复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

![image-20210326051346587](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326051346.png)

### 思路

#### （1）原地复制节点

![image-20210326052344039](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326052344.png)

![image-20210326052355099](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326052355.png)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head==null) {
            return null;
        }
        Node p = head;
        //第一步，在每个原节点后面创建一个新节点
        //1->1'->2->2'->3->3'
        while(p!=null) {
            Node newNode = new Node(p.val);
            newNode.next = p.next;
            p.next = newNode;
            p = newNode.next;
        }
        p = head;
        //第二步，设置新节点的随机节点
        while(p!=null) {
            if(p.random!=null) {
                p.next.random = p.random.next;
            }
            p = p.next.next;
        }
        Node dummy = new Node(-1);
        p = head;
        Node cur = dummy;
        //第三步，将两个链表分离
        while(p!=null) {
            cur.next = p.next;
            cur = cur.next;
            p.next = cur.next;
            p = p.next;
        }
        return dummy.next;
    }
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/copy-list-with-random-pointer/solution/liang-chong-shi-xian-tu-jie-138-fu-zhi-dai-sui-ji-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210326052605783](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210326052606.png)

#### （2）哈希表

![image-20210327042908170](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327042908.png)

![image-20210327043010557](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327043010.png)

![image-20210327043220354](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327043220.png)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if(head==null) {
            return null;
        }
        //创建一个哈希表，key是原节点，value是新节点
        Map<Node,Node> map = new HashMap<Node,Node>();
        Node p = head;
        //将原节点和新节点放入哈希表中
        while(p!=null) {
            Node newNode = new Node(p.val);
            map.put(p,newNode);
            p = p.next;
        }
        p = head;
        //遍历原链表，设置新节点的next和random
        while(p!=null) {
            Node newNode = map.get(p);
            //p是原节点，map.get(p)是对应的新节点，p.next是原节点的下一个
            //map.get(p.next)是原节点下一个对应的新节点
            if(p.next!=null) {
                newNode.next = map.get(p.next);
            }
            //p.random是原节点随机指向
            //map.get(p.random)是原节点随机指向  对应的新节点 
            if(p.random!=null) {
                newNode.random = map.get(p.random);
            }
            p = p.next;
        }
        //返回头结点，即原节点对应的value(新节点)
        return map.get(head);
    }
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/copy-list-with-random-pointer/solution/liang-chong-shi-xian-tu-jie-138-fu-zhi-dai-sui-ji-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 66、  [快乐数](https://leetcode-cn.com/problems/happy-number/)

![image-20210327051136321](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327051136.png)

### 思路

#### （1）哈希表

![image-20210327054431860](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327054432.png)

![image-20210327054616084](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327054616.png)

![image-20210327054710002](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327054710.png)

```java
class Solution {
    private int getNext(int n) {
        int totalSum = 0;
        while (n > 0) {
            int d = n % 10;
            n = n / 10;
            totalSum += d * d;
        }
        return totalSum;
    }

    public boolean isHappy(int n) {
        Set<Integer> seen = new HashSet<>();
        while (n != 1 && !seen.contains(n)) {
            seen.add(n);
            n = getNext(n);
        }
        return n == 1;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/happy-number/solution/kuai-le-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210327054816223](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327054816.png)

#### （2）快慢指针找环

![image-20210327060319449](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327060319.png)

<img src="https://assets.leetcode-cn.com/solution-static/202/2.jpg" alt="img" style="zoom:67%;" />

<img src="https://assets.leetcode-cn.com/solution-static/202/3.jpg" alt="img" style="zoom:67%;" />

<img src="https://assets.leetcode-cn.com/solution-static/202/4.jpg" alt="img" style="zoom: 67%;" />![img](https://assets.leetcode-cn.com/solution-static/202/5.jpg)

<img src="https://assets.leetcode-cn.com/solution-static/202/4.jpg" alt="img" style="zoom: 67%;" />![img](https://assets.leetcode-cn.com/solution-static/202/5.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/6.jpg)

<img src="https://assets.leetcode-cn.com/solution-static/202/4.jpg" alt="img" style="zoom: 67%;" />![img](https://assets.leetcode-cn.com/solution-static/202/5.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/6.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/7.jpg)

<img src="https://assets.leetcode-cn.com/solution-static/202/4.jpg" alt="img" style="zoom: 67%;" />![img](https://assets.leetcode-cn.com/solution-static/202/5.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/6.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/7.jpg)![img](https://assets.leetcode-cn.com/solution-static/202/8.jpg)

![img](https://assets.leetcode-cn.com/solution-static/202/8.jpg)

![image-20210327154718285](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327154718.png)

###### **复杂度分析**

![image-20210327154804558](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327154804.png)



## 67、[同构字符串](https://leetcode-cn.com/problems/isomorphic-strings/)

![image-20210327155235948](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327155236.png)

### 思路

#### （1）哈希表

![image-20210327162632739](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327162633.png)

```java
private boolean isIsomorphicHelper(String s, String t) {
    int n = s.length();
    HashMap<Character, Character> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c1 = s.charAt(i);
        char c2 = s.charAt(i);
        if (map.containsKey(c1)) {
            if (map.get(c1) != c2) {
                return false;
            }
        } else {
            map.put(c1, c2);
        }
    }
    return true;
}

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210327175042030](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327175042.png)

```java
public boolean isIsomorphic(String s, String t) {
    return isIsomorphicHelper(s, t) && isIsomorphicHelper(t, s);

}

private boolean isIsomorphicHelper(String s, String t) {
    int n = s.length();
    HashMap<Character, Character> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c1 = s.charAt(i);
        char c2 = t.charAt(i);
        if (map.containsKey(c1)) {
            if (map.get(c1) != c2) {
                return false;
            }
        } else {
            map.put(c1, c2);
        }
    }
    return true;
}

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）数组索引

![image-20210327175516897](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327175517.png)

```java
public boolean isIsomorphic(String s, String t) {
    return isIsomorphicHelper(s).equals(isIsomorphicHelper(t));
}

private String isIsomorphicHelper(String s) {
    int[] map = new int[128];
    int n = s.length();
    StringBuilder sb = new StringBuilder();
    int count = 1;
    for (int i = 0; i < n; i++) {
        char c = s.charAt(i);
        //当前字母第一次出现,赋值
        if (map[c] == 0) {
            map[c] = count;
            count++;
        }
        sb.append(map[c]);
    }
    return sb.toString();
}

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210327175640296](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327175640.png)

```java
public boolean isIsomorphic(String s, String t) {
    return isIsomorphicHelper(s).equals(isIsomorphicHelper(t));
}

private String isIsomorphicHelper(String s) {
    int[] map = new int[128];
    int n = s.length();
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < n; i++) {
        char c = s.charAt(i);
        //当前字母第一次出现,赋值
        if (map[c] == 0) {
            map[c] = i + 1;
        }
        sb.append(map[c]);
    }
    return sb.toString();
}

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

> 其实我们不需要将字符串完全转换，我们可以用两个 map 分别记录两个字符串每个字母的映射。将所有字母初始都映射到 0。记录过程中，如果发现了当前映射不一致，就可以立即返回 false 了。
>
> 举个例子。

```json
对 abaddee 和 cdbccff
    
a b a d d e e
c d b c c f f
^

当前
a -> 0
c -> 0

修改映射
a -> 1
c -> 1

a b a d d e e
c d b c c f f
  ^
    
当前
b -> 0
d -> 0   
    
修改映射
b -> 2
d -> 2

    
a b a d d e e
c d b c c f f
    ^    
当前
a -> 1 (之前被修改过)
b -> 0

出现不一致，所以两个字符串不异构 

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

> 代码的话，用两个 `map` 记录映射即可。

```java
public boolean isIsomorphic(String s, String t) {
    int n = s.length();
    int[] mapS = new int[128];
    int[] mapT = new int[128];
    for (int i = 0; i < n; i++) {
        char c1 = s.charAt(i);
        char c2 = t.charAt(i);
        //当前的映射值是否相同
        if (mapS[c1] != mapT[c2]) {
            return false;
        } else {
            //是否已经修改过，修改过就不需要再处理
            if (mapS[c1] == 0) { //这时候是相等的，所以只要判断一个数组的值即可
                mapS[c1] = i + 1;  //用索引+1来标识当前位置的字母
                mapT[c2] = i + 1;
            }
        }
    }
    return true;
}

作者：windliang
链接：https://leetcode-cn.com/problems/isomorphic-strings/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-42/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 68、[存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

![image-20210327183520933](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327183521.png)

### 思路

#### （1）排序

![image-20210327183555169](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327183555.png)

#### （2）哈希表

![image-20210327183611864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327183612.png)

## 69、[有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

![image-20210327185330073](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327185330.png)

### 思路

#### （1）数组索引

![image-20210327185442823](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327185443.png)

#### （2）排序

![image-20210327190044959](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327190045.png)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        char[] str1 = s.toCharArray();
        char[] str2 = t.toCharArray();
        Arrays.sort(str1);
        Arrays.sort(str2);
        return Arrays.equals(str1, str2);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/valid-anagram/solution/you-xiao-de-zi-mu-yi-wei-ci-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）哈希表

![image-20210327190450986](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327190451.png)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        Map<Character, Integer> table = new HashMap<Character, Integer>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            table.put(ch, table.getOrDefault(ch, 0) + 1);
        }
        for (int i = 0; i < t.length(); i++) {
            char ch = t.charAt(i);
            table.put(ch, table.getOrDefault(ch, 0) - 1);
            if (table.get(ch) < 0) {
                return false;
            }
        }
        return true;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/valid-anagram/solution/you-xiao-de-zi-mu-yi-wei-ci-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210327190548279](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210327190548.png)

## 70、[猜数字游戏](https://leetcode-cn.com/problems/bulls-and-cows/)

![image-20210328033210976](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328033211.png)

### 思路

![image-20210328041337492](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328041338.png)

#### （1）哈希表

![image-20210328041543866](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328041544.png)

![image-20210328041806220](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328041806.png)

```java
public String getHint(String secret, String guess) {
    int A = 0;
    for (int i = 0; i < guess.length(); i++) {
        if (secret.charAt(i) == guess.charAt(i)) {
            A++;
        }
    }
    HashMap<Character, Integer> mapS = new HashMap<>();
    HashMap<Character, Integer> mapG = new HashMap<>();
    for (int i = 0; i < secret.length(); i++) {
        mapS.put(secret.charAt(i), mapS.getOrDefault(secret.charAt(i), 0) + 1);
        mapG.put(guess.charAt(i), mapG.getOrDefault(guess.charAt(i), 0) + 1);
    }
    int B = 0;
    for (Character key : mapG.keySet()) {
        int n1 = mapG.getOrDefault(key, 0);
        int n2 = mapS.getOrDefault(key, 0);
        B = B + Math.min(n1, n2);
    }
    return A + "A" + (B - A) + "B";
}

作者：windliang
链接：https://leetcode-cn.com/problems/bulls-and-cows/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--55/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）两个数组优化

![image-20210328042003945](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328042004.png)

#### （3）单个数组for循环

![image-20210328043604437](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328043604.png)

![image-20210328043748324](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328043748.png)

```json
"231" 和 "321"

secret 2  3  1
guess  3  2  1
       ^
       i

当前遍历到第 1 个数, s = 2, g = 3
numbers[s]++, 也就是 numbers[2] = 1
numbers[g]--, 也就是 numbers[3] = -1    
    
secret 2  3  2
guess  3  2  1
          ^
          i

numbers[2] = 1, numbers[3] = -1
    
当前遍历到第 2 个数, s = 3, g = 2
此时 numbers[s], 也就是 numbers[3] < 0, cows++
此时 numbers[g], 也就是 numbers[2] > 0, cows++
cows = 2

继续执行
numbers[s]++, 也就是 numbers[3] + 1 = -1 + 1 = 0
numbers[g]--, 也就是 numbers[2] - 1 = 1 - 1 = 0

secret 2  3  2
guess  3  2  1
             ^
             i
numbers[2] = 0, numbers[3] = 0    
当前遍历到第 3 个数, s = 2, g = 1    
此时 s 虽然又遇到了 2, 但是 number[2] = 0 了, 无法再匹配, 所以 cows 不会加 1 了 

最终 cows 就是 2 了

作者：windliang
链接：https://leetcode-cn.com/problems/bulls-and-cows/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--55/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
public String getHint(String secret, String guess) {
    int bulls = 0;
    int cows = 0;
    int[] numbers = new int[10];
    for (int i = 0; i<secret.length(); i++) {
        int s = secret.charAt(i) - '0';
        int g = guess.charAt(i)) - '0';
        if (s == g) bulls++;
        else {
            //当前数小于 0, 说明之前在 guess 中出现过, 和 secret 当前的数匹配
            if (numbers[s] < 0) cows++;
            //当前数大于 0, 说明之前在 secret 中出现过, 和 guess 当前的数匹配
            if (numbers[g] > 0) cows++;
            //secret 中的数, 计数加 1
            numbers[s] ++;
            //guess 中的数, 计数减 1
            numbers[g] --;
        }
    }
     StringBuilder stringBuilder = new StringBuilder();
     return stringBuilder.append(bulls).append('A').append(cows).append('B').toString();
}

作者：windliang
链接：https://leetcode-cn.com/problems/bulls-and-cows/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--55/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 71、[前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

![image-20210328055652467](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328055652.png)

### 思路

#### （1）最小堆排序

![image-20210328061923426](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328061923.png)

![堆中的元素就是前 k 个频率最大的元素](https://pic.leetcode-cn.com/b548a3796066fa7072baa2b1e06e0d54641a7913d87c88c61d73b6b9ad0e90db-file_1561712388100)

![image-20210328062100877](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328062101.png)

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 使用字典，统计每个元素出现的次数，元素为键，元素出现的次数为值
        HashMap<Integer,Integer> map = new HashMap();
        for(int num : nums){
            if (map.containsKey(num)) {
               map.put(num, map.get(num) + 1);
             } else {
                map.put(num, 1);
             }
        }
        // 遍历map，用最小堆保存频率最大的k个元素
        PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer a, Integer b) {
                return map.get(a) - map.get(b);
            }
        });
        for (Integer key : map.keySet()) {
            if (pq.size() < k) {
                pq.add(key);
            } else if (map.get(key) > map.get(pq.peek())) {
                pq.remove();
                pq.add(key);
            }
        }
        // 取出最小堆中的元素
        int [] res=new int[k];
         for(int i=0;i<k;i++){
             res[i]=pq.remove();
         }
         return res;
    }
}


作者：MisterBooo
链接：https://leetcode-cn.com/problems/top-k-frequent-elements/solution/leetcode-di-347-hao-wen-ti-qian-k-ge-gao-pin-yuan-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210328062359717](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328062400.png)

#### （2）桶排序

![image-20210328062437870](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328062438.png)

![image-20210328223328370](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328223328.png)

```java
//基于桶排序求解「前 K 个高频元素」
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> res = new ArrayList();
        // 使用字典，统计每个元素出现的次数，元素为键，元素出现的次数为值
        HashMap<Integer,Integer> map = new HashMap();
        for(int num : nums){
            if (map.containsKey(num)) {
               map.put(num, map.get(num) + 1);
             } else {
                map.put(num, 1);
             }
        }
        
        //桶排序
        //将频率作为数组下标，对于出现频率不同的数字集合，存入对应的数组下标
        List<Integer>[] list = new List[nums.length+1];
        for(int key : map.keySet()){
            // 获取出现的次数作为下标
            int i = map.get(key);
            if(list[i] == null){
               list[i] = new ArrayList();
            } 
            list[i].add(key);
        }
        
        // 倒序遍历数组获取出现顺序从大到小的排列
        for(int i = list.length - 1;i >= 0 && res.size() < k;i--){
            if(list[i] == null) continue;
            res.addAll(list[i]);
        }
        return res;
    }
}

作者：MisterBooo
链接：https://leetcode-cn.com/problems/top-k-frequent-elements/solution/leetcode-di-347-hao-wen-ti-qian-k-ge-gao-pin-yuan-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210328223403149](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328223403.png)

#### （3）stream流的桶排序（计数）

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 统计每个数字出现的次数
        Map<Integer, Integer> counterMap = IntStream.of(nums).boxed().collect(Collectors.toMap(e -> e, e -> 1, Integer::sum));
        // 一个数字最多出现 nums.length 次，因此定义一个长度为 nums.length + 1 的数组，freqList[i] 中存储出现次数为 i 的所有数字。
        List<Integer>[] freqList = new List[nums.length + 1];
        for (int i = 0; i < freqList.length; i++) {
            freqList[i] = new ArrayList<>();
        }
        counterMap.forEach((num, freq) -> {
            freqList[freq].add(num);
        });
        // 按照出现频次，从大到小遍历频次数组，构造返回结果。
        int[] res = new int[k];
        int idx = 0;
        for (int freq = freqList.length - 1; freq > 0; freq--) {
            for (int num: freqList[freq]) {
                res[idx++] = num;
                if (idx == k) {
                    return res;
                }
            }
        }
        return res;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/top-k-frequent-elements/solution/4-chong-fang-fa-miao-sha-topkji-shu-pai-xu-kuai-pa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210328225353316](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210328225353.png)

#### （4）二叉搜索树

![image-20210330051213833](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330051217.png)

![image-20210330051308818](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330051309.png)

![image-20210330051639939](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330051640.png)

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 统计每个数字出现的次数
        Map<Integer, Integer> counterMap = IntStream.of(nums).boxed().collect(Collectors.toMap(e -> e, e -> 1, Integer::sum));
        // 定义二叉搜索树：key 是数字出现的次数，value 是出现了key次的数字列表。
        TreeMap<Integer, List<Integer>> treeMap = new TreeMap<>();
        // 维护一个有 k 个数字的二叉搜索树：
        // 不足 k 个直接将当前数字加入到树中；否则判断当前树中的最小次数是否小于当前数字的出现次数，若是，则删掉树中出现次数最少的一个数字，将当前数字加入树中。
        int count = 0;
        for(Map.Entry<Integer, Integer> entry: counterMap.entrySet()) {
            int num = entry.getKey();
            int cnt = entry.getValue();
            if (count < k) {
                treeMap.computeIfAbsent(cnt, ArrayList::new).add(num);
                count++;
            } else {
                Map.Entry<Integer, List<Integer>> firstEntry = treeMap.firstEntry();
                if (cnt > firstEntry.getKey()) {
                    treeMap.computeIfAbsent(cnt, ArrayList::new).add(num);
                    List<Integer> list = firstEntry.getValue();
                    if (list.size() == 1) {
                        treeMap.pollFirstEntry();
                    } else {
                        list.remove(list.size() - 1);
                    }
                }
            }
        }
        // 构造返回结果
        int[] res = new int[k];
        int idx = 0;
        for (List<Integer> list: treeMap.values()) {
            for (int num: list) {
                res[idx++] = num;
            }
        }
        return res;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/top-k-frequent-elements/solution/4-chong-fang-fa-miao-sha-topkji-shu-pai-xu-kuai-pa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210330053708260](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330053708.png)

#### （5）快排

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 统计每个数字出现的次数
        Map<Integer, Integer> counterMap = IntStream.of(nums).boxed().collect(Collectors.toMap(e -> e, e -> 1, Integer::sum));
        // 构造Pair数组，Pair.num 表示数值，Pair.freq 表示数字出现的次数
        Pair[] pairs = IntStream.of(nums).distinct().boxed().map(num -> new Pair(num, counterMap.get(num))).toArray(Pair[]::new);
        // 使用快排变形 O(N) 获取Pair数组中频次最大的 k 个元素（第 4 个参数是下标，因此是 k - 1）。
        Pair[] topKPairs = quickSelect(pairs, 0, pairs.length - 1, k - 1);
        
        // 构造返回结果
        int[] res = new int[k];
        int idx = 0;
        for (Pair pair: topKPairs) {
            res[idx++] = pair.num;
        }
        return res;
    }

    private Pair[] quickSelect(Pair[] pairs, int lo, int hi, int idx) {
        if (lo > hi) {
            return new Pair[0];
        }
        // 快排切分后，j 左边的数字出现的次数都大于等于 pairs[j].freq，j 右边的数字出现的次数都小于等于 pairs[j].freq。
        int j = partition(pairs, lo, hi);
        // 如果 j 正好等于目标idx，说明当前pairs数组中的[0, idx]就是出现次数最大的 K 个元素。
        if (j == idx) {
            return Arrays.copyOf(pairs, idx + 1);
        }
        // 否则根据 j 与 idx 的大小关系判断找左段还是右段
        return j < idx? quickSelect(pairs, j + 1, hi, idx): quickSelect(pairs, lo, j - 1, idx);
    }

    private int partition(Pair[] pairs, int lo, int hi) {
        Pair v = pairs[lo];
        int i = lo, j = hi + 1;
        while (true) {
            while(++i <= hi && pairs[i].freq > v.freq);
            while(--j >= lo && pairs[j].freq < v.freq);
            if (i >= j) {
                break;
            }
            Pair tmp = pairs[i];
            pairs[i] = pairs[j];
            pairs[j] = tmp;
        }
        pairs[lo] = pairs[j];
        pairs[j] = v;
        return j;
    }
}

class Pair {
    int num;
    int freq;

    public Pair(int num, int freq) {
        this.num = num;
        this.freq = freq;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/top-k-frequent-elements/solution/4-chong-fang-fa-miao-sha-topkji-shu-pai-xu-kuai-pa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 72、[两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

![image-20210330062840639](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330062841.png)

### 思路

#### （1）**Set**

![image-20210330062946607](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330062947.png)

```java
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> resSet = new HashSet<>();
        //把nums1中的元素全部加入到集合set1中
        for (int i = 0; i < nums1.length; i++) {
            set1.add(nums1[i]);
        }

        for (int i = 0; i < nums2.length; i++) {
            //查看nums2中的元素是否存在集合set1中，如果存在，
            //就加入到集合resSet中
            if (set1.contains(nums2[i])) {
                resSet.add(nums2[i]);
            }
        }
        //下面一大坨是把集合reSet转换为数组
        int i = 0;
        int[] res = new int[resSet.size()];
        for (Integer num : resSet) {
            res[i++] = num;
        }
        return res;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/intersection-of-two-arrays/solution/zhi-jie-ji-suan-he-pai-xu-yi-ji-er-fen-fa-cha-zhao/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### （2）**双指针**

![image-20210330064002756](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330064003.png)

![image-20210330063940959](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210330063941.png)

```java
public int[] intersection(int[] nums1, int[] nums2) {
  Set<Integer> set = new HashSet<>();
  Arrays.sort(nums1);
  Arrays.sort(nums2);
  int i = 0, j = 0;
  while (i < nums1.length && j < nums2.length) {
    if (nums1[i] == nums2[j]) {
      set.add(nums1[i]);
      i++;
      j++;
    } else if (nums1[i] < nums2[j]) {
      i++;
    } else if (nums1[i] > nums2[j]) {
      j++;
    }
  }
  int[] res = new int[set.size()];
  int index = 0;
  for (int num : set) {
    res[index++] = num;
  }
  return res;
}

作者：a-fei-8
链接：https://leetcode-cn.com/problems/intersection-of-two-arrays/solution/duo-chong-jie-fa-jie-jue-349-liang-ge-shu-zu-de-ji/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）**二分查找**

```java
public int[] intersection(int[] nums1, int[] nums2) {
  Set<Integer> set = new HashSet<>();
  Arrays.sort(nums2);
  for (int target : nums1) {
    if (binarySearch(nums2, target) && !set.contains(target)) {
      set.add(target);
    }
  }
  int index = 0;
  int[] res = new int[set.size()];
  for (int num : set) {
    res[index++] = num;
  }
  return res;
}
public boolean binarySearch(int[] nums, int target) {
  int left = 0, right = nums.length - 1;
  while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) {
      return true;
    } else if (nums[mid] > target) {
      right = mid - 1;
    } else if (nums[mid] < target) {
      left = mid + 1;
    }
  }
  return false;
}

作者：a-fei-8
链接：https://leetcode-cn.com/problems/intersection-of-two-arrays/solution/duo-chong-jie-fa-jie-jue-349-liang-ge-shu-zu-de-ji/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 73、[两个数组的交集 II](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)

![image-20210331040904143](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331040906.png)

### 思路

#### （1）排序 + 双指针

![image-20210331041204074](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331041204.png)

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        int length1 = nums1.length, length2 = nums2.length;
        int[] intersection = new int[Math.min(length1, length2)];
        int index1 = 0, index2 = 0, index = 0;
        while (index1 < length1 && index2 < length2) {
            if (nums1[index1] < nums2[index2]) {
                index1++;
            } else if (nums1[index1] > nums2[index2]) {
                index2++;
            } else {
                intersection[index] = nums1[index1];
                index1++;
                index2++;
                index++;
            }
        }
        return Arrays.copyOfRange(intersection, 0, index);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/solution/liang-ge-shu-zu-de-jiao-ji-ii-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210331041256569](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331041256.png)

#### （2）哈希表

![image-20210331041839591](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331041840.png)

![fig1](https://assets.leetcode-cn.com/solution-static/350/350_fig1.gif)

![image-20210331044142991](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331044143.png)

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return intersect(nums2, nums1);
        }
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int num : nums1) {
            int count = map.getOrDefault(num, 0) + 1;
            map.put(num, count);
        }
        int[] intersection = new int[nums1.length];
        int index = 0;
        for (int num : nums2) {
            int count = map.getOrDefault(num, 0);
            if (count > 0) {
                intersection[index++] = num;
                count--;
                if (count > 0) {
                    map.put(num, count);
                } else {
                    map.remove(num);
                }
            }
        }
        return Arrays.copyOfRange(intersection, 0, index);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/solution/liang-ge-shu-zu-de-jiao-ji-ii-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210331044219143](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331044219.png)

## 74、[剑指 Offer 第一个只出现一次的字符](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

![image-20210331071027947](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331071028.png)

### 思路

#### （1）哈希表

![image-20210331071133092](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331071133.png)

![image-20210331071302677](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331071303.png)

```java
class Solution {
    public char firstUniqChar(String s) {
        HashMap<Character, Boolean> dic = new HashMap<>();
        char[] sc = s.toCharArray();
        for(char c : sc)
            dic.put(c, !dic.containsKey(c));
        for(char c : sc)
            if(dic.get(c)) return c;
        return ' ';
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/solution/mian-shi-ti-50-di-yi-ge-zhi-chu-xian-yi-ci-de-zi-3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析：

![image-20210331071335833](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331071336.png)

#### （2）数组实现

```java
   public char firstUniqChar(String s) {
        int[] arr = new int[26];
        char[] chars = s.toCharArray();
        for (char ch : chars){
            arr[ch -'a'] ++;
        }
        for (char c:chars){
            if (arr[c-'a'] == 1){
                return c;
            }
        }
        return ' ';
    }
```

![image-20210331071600693](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210331071601.png)





# BFS

## 75、[二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

![image-20210401052329687](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401052334.png)

### 思路

> 本题是让我们把二叉树的每一层节点放入到同一个列表中，最后返回各层的列表组成的总的列表。
>
> 可以使用 BFS 和 DFS 解决。
>
> 左边是BFS，按照层进行搜索；图右边是DFS，先一路走到底，然后再回头搜索。





![BFS-and-DFS-Algorithms.png](https://pic.leetcode-cn.com/75fc42a2cfacf6e41a86b34b1861d2cdcd2965b20d8ebc0a6dcc41bb1fbcea31-BFS-and-DFS-Algorithms.png)

#### （1）BFS

![image-20210401052550899](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401052551.png)

虽然 DFS 与 BFS 都是将二叉树的所有结点遍历了一遍，但它们遍历结点的顺序不同。

![DFS 与 BFS 对比](https://pic.leetcode-cn.com/fdcd3bd27f4008948084f6ec86b58535e71f66862bd89a34bd6fe4cc42d68e89.gif)

![image-20210401052726181](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401052726.png)

这样，我们就将 BFS 遍历改造成了层序遍历。在遍历的过程中，结点进队列和出队列的过程为：

![img](https://pic.leetcode-cn.com/94cd1fa999df0276f1dae77a9cca83f4cabda9e2e0b8571cd9550a8ee3545f56.gif)

![image-20210401052919003](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401052919.png)

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();

    Queue<TreeNode> queue = new ArrayDeque<>();
    if (root != null) {
        queue.add(root);
    }
    while (!queue.isEmpty()) {
        int n = queue.size();
        List<Integer> level = new ArrayList<>();
        for (int i = 0; i < n; i++) { 
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
        res.add(level);
    }

    return res;
}

作者：nettee
链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal/solution/bfs-de-shi-yong-chang-jing-zong-jie-ceng-xu-bian-l/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### （2）DFS

![image-20210401053549192](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401053549.png)

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res  = new ArrayList<>();
        if(root != null){
            dfs(res, root, 0);
        }
        return res;
    }
    
    private void dfs(List<List<Integer>> res, TreeNode node, int level){
        if(res.size() - 1 < level){
            res.add(new ArrayList<Integer>());
        }
        res.get(level).add(node.val);
        if(node.left!=null){
            dfs(res, node.left, level + 1);
        }
        if(node.right!=null){
            dfs(res, node.right, level + 1);
        }
    }
}

作者：fuxuemingzhu
链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal/solution/tao-mo-ban-bfs-he-dfs-du-ke-yi-jie-jue-by-fuxuemin/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![0203_1.gif](https://pic.leetcode-cn.com/aeed09e12573ec00d83663bb4f77562e8904ac58cdb2cbe6e995f2ac33b12934-0203_1.gif)



## 76、[二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

![image-20210401060644424](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401060644.png)

### 思路

#### （1）BFS

![image-20210401061245896](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401061246.png)

#### （2）BFS-2

![image-20210401063054150](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401063054.png)

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if(root==null) {
            return new ArrayList<List<Integer>>();
        }
        //改用链表实现，每次都插入到第一位
        LinkedList<List<Integer>> res = new LinkedList<List<Integer>>();
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        while(!queue.isEmpty()) {
            int size = queue.size();
            ArrayList<Integer> tmp = new ArrayList<Integer>();
            for(int i=0;i<size;++i) {
                TreeNode node = queue.poll();
                tmp.add(node.val);
                if(node.left!=null) {
                    queue.offer(node.left);
                }
                if(node.right!=null) {
                    queue.offer(node.right);
                }
            }
            //每次都保存到链表的第一位，这样自带了翻转的功能
            res.add(0,tmp);
        }
        return res;
    }
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/solution/san-chong-shi-xian-tu-jie-107er-cha-shu-de-ceng-ci/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 77、[二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

![image-20210401064949703](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401064950.png)

### 思路

#### （1）BFS

![image-20210401065449963](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401065450.png)

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null)
            return result;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        // 记录是否反转
        boolean isReverse = false;
        while (!queue.isEmpty()) {
            LinkedList<Integer> oneLevel = new LinkedList<>();
            // 每次都取出一层的所有数据
            int count = queue.size();
            for (int i = 0; i < count; i++) {
                TreeNode node = queue.poll();
                if (!isReverse)
                    oneLevel.add(node.val);
                else
                    oneLevel.addFirst(node.val);
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
            }
            isReverse = !isReverse;
            result.add(oneLevel);
        }
        return result;
    }
}

作者：wiliSuse
链接：https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/solution/1ms-jian-ji-java-by-wilisuse/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）DFS

```java
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        travel(root, res, 0);
        return res;
    }

    private void travel(TreeNode cur, List<List<Integer>> res, int level) {
        if (cur == null)
            return;
        //如果res.size() <= level说明下一层的集合还没创建，所以要先创建下一层的集合
        if (res.size() <= level) {
            List<Integer> newLevel = new LinkedList<>();
            res.add(newLevel);
        }
        //遍历到第几层我们就操作第几层的数据
        List<Integer> list = res.get(level);
        //这里默认根节点是第0层，偶数层相当于从左往右遍历，
        // 所以要添加到集合的末尾，如果是奇数层相当于从右往左遍历，
        // 要把数据添加到集合的开头
        if (level % 2 == 0)
            list.add(cur.val);
        else
            list.add(0, cur.val);
        //分别遍历左右两个子节点，到下一层了，所以层数要加1
        travel(cur.left, res, level + 1);
        travel(cur.right, res, level + 1);
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/solution/bfshe-dfsliang-chong-jie-jue-fang-shi-by-184y/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 78、[二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

![image-20210401072008426](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401072008.png)

### 思路

#### （1）BFS

![image-20210401072111860](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401072112.png)

#### （2）DFS

![image-20210401072427559](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401072428.png)

```java
class Solution {
    List<Integer> res = new ArrayList<>();

    public List<Integer> rightSideView(TreeNode root) {
        dfs(root, 0); // 从根节点开始访问，根节点深度是0
        return res;
    }

    private void dfs(TreeNode root, int depth) {
        if (root == null) {
            return;
        }
        // 先访问 当前节点，再递归地访问 右子树 和 左子树。
        if (depth == res.size()) {   // 如果当前节点所在深度还没有出现在res里，说明在该深度下当前节点是第一个被访问的节点，因此将当前节点加入res中。
            res.add(root.val);
        }
        depth++;
        dfs(root.right, depth);
        dfs(root.left, depth);
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/binary-tree-right-side-view/solution/jian-dan-bfsdfs-bi-xu-miao-dong-by-sweetiee/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 79、[岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

![image-20210401224555925](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401224558.png)

### 思路

> DFS就是沿着一条路径一直走下去，当遇到终止条件的时候才会返回，而BFS就是先把当前位置附近的访问一遍，就像下面这样先访问圈内的，然后再把圈放大继续访问，就像下面这样

![image.png](https://pic.leetcode-cn.com/1602049230-IHuJxA-image.png)

#### （1）DFS网格类遍历

![image-20210401230849621](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401230850.png)

<img src="https://pic.leetcode-cn.com/5dae0de2a06f4eae5113f9cadfa5c51bbcf0b9347c5861aa73c93d7bc1d50b34-image.png" alt="img" style="zoom: 67%;" />

<img src="https://pic.leetcode-cn.com/61a98091fe75de107bf886a608e87d5bb29d810b9e7eb1e00c541f49194ea543-image.png" alt="img" style="zoom:67%;" />

<img src="https://pic.leetcode-cn.com/6d060f275561dc390841f78d85124fd193efb0801ea2dc865b2767f1093e4f3a-image.png" alt="img" style="zoom:67%;" />![img](https://pic.leetcode-cn.com/233368eab82832268c3c7266fd7fe65c78626de9fd89cb90b3f36a7b4e52860d-image.png)

<img src="https://pic.leetcode-cn.com/233368eab82832268c3c7266fd7fe65c78626de9fd89cb90b3f36a7b4e52860d-image.png" alt="img" style="zoom:67%;" />

![img](https://pic.leetcode-cn.com/bf055c3d5f6b310f6a110ada65d503de6d7793276f5638731c81c53b4276b835-image.png)

![img](https://pic.leetcode-cn.com/195ba530a5350e0a8f8e372319d061c85316c1064017d5c00d7cfc2b0c055de6-image.png)

![img](https://pic.leetcode-cn.com/c21a88bde9450ad83d6164c2df4677b16154fa5990408459a2a45982b9cd70fd-image.png)

![img](https://pic.leetcode-cn.com/0cfddec2d15ed9a56c434f470ff07eb3dfc83dccbda0160583d1bce469077274-image.png)

![image-20210401231445468](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231445.png)

![image-20210401231519705](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231520.png)

![image-20210401231541913](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231542.png)

![image-20210401231708865](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231709.png)

###### 如何避免重复遍历

![image-20210401231740652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231741.png)

![image-20210401231810862](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210401231811.png)

![标记已遍历的格子](https://pic.leetcode-cn.com/20fe202fb5e5fc5048e140c29310c1bcbb17661860d2441e8a3feb1236a2e44d.gif)

```java
class Solution {
    void dfs(char[][] grid, int r, int c) {
        int nr = grid.length;
        int nc = grid[0].length;

        if (r < 0 || c < 0 || r >= nr || c >= nc || grid[r][c] == '0') {
            return;
        }

        grid[r][c] = '0';
        dfs(grid, r - 1, c);
        dfs(grid, r + 1, c);
        dfs(grid, r, c - 1);
        dfs(grid, r, c + 1);
    }

    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }

        int nr = grid.length;
        int nc = grid[0].length;
        int num_islands = 0;
        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {
                    ++num_islands;
                    dfs(grid, r, c);
                }
            }
        }

        return num_islands;
    }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-shu-liang-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）广度优先遍历 BFS

![image-20210402055324861](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210402055325.png)

![image-20210402055436888](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210402055437.png)

```java
class Solution {
    public int numIslands(char[][] grid) {
        int count = 0;
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[0].length; j++) {
                if(grid[i][j] == '1'){
                    bfs(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }
    private void bfs(char[][] grid, int i, int j){
        Queue<int[]> list = new LinkedList<>();
        list.add(new int[] { i, j });
        while(!list.isEmpty()){
            int[] cur = list.remove();
            i = cur[0]; j = cur[1];
            if(0 <= i && i < grid.length && 0 <= j && j < grid[0].length && grid[i][j] == '1') {
                grid[i][j] = '0';
                list.add(new int[] { i + 1, j });
                list.add(new int[] { i - 1, j });
                list.add(new int[] { i, j + 1 });
                list.add(new int[] { i, j - 1 });
            }
        }
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/number-of-islands/solution/number-of-islands-shen-du-you-xian-bian-li-dfs-or-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution {
	
    //这样就是为了获取一个点的上下左右的坐标
    private final static int[][] DIRECTIONS = {{-1, 0}, {0, -1}, {1, 0}, {0, 1}};
    private int rows;
    private int cols;
    private char[][] grid;
    private boolean[][] visited;

    public int numIslands(char[][] grid) {
        rows = grid.length;
        if (rows == 0) {
            return 0;
        }
        cols = grid[0].length;
        this.grid = grid;
        visited = new boolean[rows][cols];

        int count = 0;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (!visited[i][j] && grid[i][j] == '1') {
                    bfs(i, j);
                    count++;
                }
            }
        }
        return count;
    }

    private void bfs(int i, int j) {
        Queue<Integer> queue = new LinkedList<>();
        //把两个i,j坐标转成一个值
        queue.offer(i * cols + j);
        // 注意：这里要标记上已经访问过
        visited[i][j] = true;
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            //还原i，j
            int curX = cur / cols;
            int curY = cur % cols;
            for (int k = 0; k < 4; k++) {
                int newX = curX + DIRECTIONS[k][0];
                int newY = curY + DIRECTIONS[k][1];
                if (inArea(newX, newY) && grid[newX][newY] == '1' && !visited[newX][newY]) {
                    queue.offer(newX * cols + newY);
                    // 特别注意：在放入队列以后，要马上标记成已经访问过，语义也是十分清楚的：反正只要进入了队列，迟早都会遍历到它
                    // 而不是在出队列的时候再标记，如果是出队列的时候再标记，会造成很多重复的结点进入队列，造成重复的操作，这句话如果你没有写对地方，代码会严重超时的
                    visited[newX][newY] = true;
                }
            }
        }
    }

    private boolean inArea(int x, int y) {
        return x >= 0 && x < rows && y >= 0 && y < cols;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/number-of-islands/solution/dfs-bfs-bing-cha-ji-python-dai-ma-java-dai-ma-by-l/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）并查集



## 80、[岛屿的最大面积](https://leetcode-cn.com/problems/max-![image-20210402063637933](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210402063637933.png)area-of-island/)

![image-20210402063643199](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210402063643.png)

### 思路

#### （1）DFS

![image-20210402063914844](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210402063915.png)

#### （2）深度优先搜索 + 栈

![image-20210403055704722](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403055705.png)

```java
class Solution {

    public int maxAreaOfIsland(int[][] grid) {
        Deque<int[]> stack = new LinkedList<>();

        int[][] moveIndexArray = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int maxArea = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[i].length; j++) {
                stack.push(new int[]{i, j});
                //计算最大面积
                int currMaxArea = 0;
                while (!stack.isEmpty()) {
                    int[] pop = stack.pop();
                    int currI = pop[0];
                    int currJ = pop[1];
                    if (currI < 0 || currI >= grid.length || currJ < 0 || currJ >= grid[0].length || grid[currI][currJ] == 0) {
                        continue;
                    }
                    currMaxArea++;
                    grid[currI][currJ] = 0;
                    for (int[] moveIndex : moveIndexArray) {
                        stack.push(new int[]{currI + moveIndex[0], currJ + moveIndex[1]});
                    }
                }
                maxArea = Math.max(currMaxArea, maxArea);
            }
        }

        return maxArea;
    }

}


作者：wang_dong
链接：https://leetcode-cn.com/problems/max-area-of-island/solution/javaban-3chong-jie-fa-by-wang_dong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）广度优先遍历(队列实现)

基于解法二的变种，把栈替换为队列。基于解法二的变种，把栈替换为队列。

```java
class Solution {

    public int maxAreaOfIsland(int[][] grid) {
        Deque<int[]> queue = new LinkedList<>();

        int[][] moveIndexArray = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int maxArea = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[i].length; j++) {
                queue.offer(new int[]{i, j});
                //计算最大面积
                int currMaxArea = 0;
                while (!queue.isEmpty()) {
                    int size = queue.size();
                    for (int k = 0; k < size; k++) {
                        int[] poll = queue.poll();
                        int currI = poll[0];
                        int currJ = poll[1];
                        if (currI < 0 || currI >= grid.length || currJ < 0 || currJ >= grid[0].length || grid[currI][currJ] == 0) {
                            continue;
                        }
                        currMaxArea++;
                        grid[currI][currJ] = 0;
                        for (int[] moveIndex : moveIndexArray) {
                            queue.offer(new int[]{currI + moveIndex[0], currJ + moveIndex[1]});
                        }
                    }
                }
                maxArea = Math.max(currMaxArea, maxArea);
            }
        }

        return maxArea;
    }

}

作者：wang_dong
链接：https://leetcode-cn.com/problems/max-area-of-island/solution/javaban-3chong-jie-fa-by-wang_dong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 81、[岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/)

![image-20210403060710837](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403060711.png)

### 思路

#### （1）DFS

![image-20210403065849172](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403065849.png)

![image-20210403070140105](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403070140.png)

```java
public int islandPerimeter(int[][] grid) {
    for (int r = 0; r < grid.length; r++) {
        for (int c = 0; c < grid[0].length; c++) {
            if (grid[r][c] == 1) {
                // 题目限制只有一个岛屿，计算一个即可
                return dfs(grid, r, c);
            }
        }
    }
    return 0;
}

int dfs(int[][] grid, int r, int c) {
    if (!(0 <= r && r < grid.length && 0 <= c && c < grid[0].length)) {
        return 1;
    }
    if (grid[r][c] == 0) {
        return 1;
    }
    if (grid[r][c] != 1) {
        return 0;
    }
    grid[r][c] = 2;
    return dfs(grid, r - 1, c)
        + dfs(grid, r + 1, c)
        + dfs(grid, r, c - 1)
        + dfs(grid, r, c + 1);
}

作者：nettee
链接：https://leetcode-cn.com/problems/island-perimeter/solution/tu-jie-jian-ji-er-qiao-miao-de-dfs-fang-fa-java-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）常规思路

![image-20210403071332068](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403071332.png)

```java
class Solution {
    public int islandPerimeter(int[][] grid) {
      	int sum = 0;
      	for(int i = 0; i < grid.length; i++) {
          	for(int j = 0; j < grid[0].length; j++) {
              	if(grid[i][j] == 1) {
                  	int lines = 4;
                  	//判断这个岛旁边连接了多少个岛
                  	if(i > 0 && grid[i - 1][j] == 1) lines--;
                  	if(i < grid.length - 1 && grid[i + 1][j] == 1) lines--;
                  	if(j > 0 && grid[i][j - 1] == 1) lines--;
                  	if(j < grid[0].length - 1 && grid[i][j + 1] == 1) lines--;
                  	sum += lines;
                }
            }
        }
      	return sum;
    }
}

作者：lyl0724-2
链接：https://leetcode-cn.com/problems/island-perimeter/solution/chang-gui-jie-fa-javashi-xian-by-lyl0724-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 82、[填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

![image-20210403171339733](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403171340.png)

### 思路

#### （1）迭代解法

![image-20210403171951194](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403171951.png)

> 这里我们需要保证 root.next 不为空就可以了。
> 也就是说当我们要串联第 i 层节点时，需要先完成第 i-1 层的节点串联
> 第一层最多只有一个节点，不需要串联
> 第二层最多只有两个节点，借助根节点就可以完成串联了
> 第三层串联时，上一层已经串联完了，所以第三层可以完成串联
> 同理，可以完成第四层，第五层，第N层的串联

<img src="https://pic.leetcode-cn.com/eef065800417742831e8702ef7b45ccb082afa3782470af81491d2b4b6046f40-5.gif" alt="5.gif" style="zoom:67%;" />

```java
class Solution {
	public Node connect(Node root) {
		if(root==null) {
			return root;
		}
		Node pre = root;
		//循环条件是当前节点的left不为空，当只有根节点
		//或所有叶子节点都出串联完后循环就退出了
		while(pre.left!=null) {
			Node tmp = pre;
			while(tmp!=null) {
				//将tmp的左右节点都串联起来
				//注:外层循环已经判断了当前节点的left不为空
				tmp.left.next = tmp.right;
				//下一个不为空说明上一层已经帮我们完成串联了
				if(tmp.next!=null) {
					tmp.right.next = tmp.next.left;
				}
				//继续右边遍历
				tmp = tmp.next;
			}
			//从下一层的最左边开始遍历
			pre = pre.left;
		}
		return root;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/dong-hua-yan-shi-san-chong-shi-xian-116-tian-chong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210403172305774](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403172306.png)

###### 复杂度分析

![image-20210403172334023](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403172334.png)

#### （2）递归

![image-20210403172959842](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403173000.png)

<img src="https://pic.leetcode-cn.com/1c7010a175e04043f7e88fbccd69ed15fa42b474eb2b5eac6c32ad833e0ec91c-7.gif" alt="7.gif" style="zoom: 67%;" />

```java
class Solution {
	public Node connect(Node root) {
		dfs(root);
		return root;
	}
	
	void dfs(Node root) {
		if(root==null) {
			return;
		}
		Node left = root.left;
		Node right = root.right;
		//配合动画演示理解这段，以root为起点，将整个纵深这段串联起来
		while(left!=null) {
			left.next = right;
			left = left.right;
			right = right.left;
		}
		//递归的调用左右节点，完成同样的纵深串联
		dfs(root.left);
		dfs(root.right);
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/dong-hua-yan-shi-san-chong-shi-xian-116-tian-chong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210403175314961](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403175315.png)

#### （3）迭代解法2

![image-20210403175227773](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403175228.png)

![image-20210403175540641](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403175541.png)

```java
class Solution {
	public Node connect(Node root) {
		if(root==null) {
			return root;
		}
		LinkedList<Node> queue = new LinkedList<Node>();
		queue.add(root);
		while(queue.size()>0) {
			int size = queue.size();
			//将队列中的元素串联起来
			Node tmp = queue.get(0);
			for(int i=1;i<size;++i) {
				tmp.next = queue.get(i);
				tmp = queue.get(i);
			}
			//遍历队列中的每个元素，将每个元素的左右节点也放入队列中
			for(int i=0;i<size;++i) {
				tmp = queue.remove();
				if(tmp.left!=null) {
					queue.add(tmp.left);
				}
				if(tmp.right!=null) {
					queue.add(tmp.right);
				}
			}
		}
		return root;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/dong-hua-yan-shi-san-chong-shi-xian-116-tian-chong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （4）简洁递归

![image-20210403181928951](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403181948.png)

```java

public Node connect(Node root) {
    dfs(root, null);
    return root;
}

private void dfs(Node node, Node next) {
    if(node != null) {
        node.next = next;
        dfs(node.left, node.right);
        dfs(node.right, node.next != null ? node.next.left : null);
    }
}


作者：meifans
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/java-san-xing-he-xin-dai-ma-chao-jian-ji-yi-yu-li-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （5）BFS

```java
    public Node connect(Node root) {
        if (root == null)
            return root;
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            //每一层的数量
            int levelCount = queue.size();
            //前一个节点
            Node pre = null;
            for (int i = 0; i < levelCount; i++) {
                //出队
                Node node = queue.poll();
                //如果pre为空就表示node节点是这一行的第一个，
                //没有前一个节点指向他，否则就让前一个节点指向他
                if (pre != null) {
                    pre.next = node;
                }
                //然后再让当前节点成为前一个节点
                pre = node;
                //左右子节点如果不为空就入队
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
            }
        }
        return root;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/bfshe-di-gui-zui-hou-liang-chong-ji-bai-liao-100-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （6）BFS+链表

![image-20210403184625111](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403184650.png)

![image-20210403184743856](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403184744.png)

```java
    public Node connect(Node root) {
        if (root == null)
            return root;
        //cur我们可以把它看做是每一层的链表
        Node cur = root;
        while (cur != null) {
            //遍历当前层的时候，为了方便操作在下一
            //层前面添加一个哑结点（注意这里是访问
            //当前层的节点，然后把下一层的节点串起来）
            Node dummy = new Node(0);
            //pre表示下一层节点的前一个节点
            Node pre = dummy;
            
            //然后开始遍历当前层的链表
            //因为是完美二叉树，如果有左子节点就一定有右子节点
            while (cur != null && cur.left != null) {
                //让pre节点的next指向当前节点的左子节点，也就是把它串起来
                pre.next = cur.left;
                //然后再更新pre
                pre = pre.next;

                //pre节点的next指向当前节点的右子节点，
                pre.next = cur.right;
                pre = pre.next;
                //继续访问这一行的下一个节点
                cur = cur.next;
            }
            //把下一层串联成一个链表之后，让他赋值给cur，
            //后续继续循环，直到cur为空为止
            cur = dummy.next;
        }
        return root;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/solution/bfshe-di-gui-zui-hou-liang-chong-ji-bai-liao-100-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210403185013185](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403185014.png)

# DFS

## 83、[面试题 16.19. 水域大小](https://leetcode-cn.com/problems/pond-sizes-lcci/)



![image-20210403194939122](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210403194942.png)

### 思路

#### （1）DFS

```java
class Solution {
    public int[] pondSizes(int[][] land) {
      List<Integer> list = new ArrayList<>();
      int temp;

      // 遍历矩阵每个元素
      for (int i = 0; i < land.length; i++) {
        for (int j = 0; j < land[0].length; j++) {
          temp = findPool(land, i, j);
          if (temp != 0) {
              list.add(temp);
            }
        }
      }

      // 第一种List<Integer>转int[]
      // int[] result = new int[list.size()];
      // for (int i = 0; i < result.length; i++) {
      //   result[i] = list.get(i);
      // }

      // 第二种List<Integer>转int[]，优雅且高效
      int[] result = list.stream().mapToInt(Integer::valueOf).toArray();

      Arrays.sort(result);

      return result;
    }

    private int findPool(int[][] land, int x, int y) {
      int num = 0;
      if (x < 0 || x >= land.length || y < 0 || y >= land[0].length || land[x][y] != 0) {
        return num;
      }
      num++;
      land[x][y] = -1;   // 如果为0，搜索之后就转换为-1，避免重复搜索
      //上下左右，左上左下右上右下8个方向扩散
      num += findPool(land, x + 1, y);
      num += findPool(land, x - 1, y);
      num += findPool(land, x, y + 1);
      num += findPool(land, x, y - 1);
      num += findPool(land, x + 1, y + 1);
      num += findPool(land, x + 1, y - 1);
      num += findPool(land, x - 1, y + 1);
      num += findPool(land, x - 1, y - 1);

      return num;
    }
}

作者：LeetcodeFrom20210207
链接：https://leetcode-cn.com/problems/pond-sizes-lcci/solution/mian-shi-ti-1619-shui-yu-da-xiao-by-leet-9p15/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 84、[电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

![image-20210405043626780](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405043628.png)

### 思路

#### （1）回溯+递归

![image-20210405045958941](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405045959.png)

![image-20210405050021171](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405050021.png)

![image-20210405050046801](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405050047.png)

![image-20210405060804854](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405060805.png)

![image-20210405060927397](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405060927.png)

<img src="https://pic.leetcode-cn.com/9a86e75bd66edbecf7f11e10d501910ac7d64c5642050bc52d86e5a0eaa83fd5-%E9%80%92%E5%BD%92%E5%8A%A8%E6%80%81%E5%9B%BE.gif" alt="递归动态图.gif" style="zoom:67%;" />







![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210405060713.png)

#### （2）队列

![image-20210406075320877](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406075324.png)

![image-20210406075333732](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406075334.png)

![image-20210406075346825](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406075347.png)

![队列-动态图.gif](https://pic.leetcode-cn.com/6953e7a27bff1242c37f88c9b66b524975655605d053a9f6ac6a74376582b4c5-%E9%98%9F%E5%88%97-%E5%8A%A8%E6%80%81%E5%9B%BE.gif)

![image-20210406075504938](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406075505.png)

```java
class Solution {
	public List<String> letterCombinations(String digits) {
		if(digits==null || digits.length()==0) {
			return new ArrayList<String>();
		}
		//一个映射表，第二个位置是"abc“,第三个位置是"def"。。。
		//这里也可以用map，用数组可以更节省点内存
		String[] letter_map = {
			" ","*","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"
		};
		List<String> res = new ArrayList<>();
		//先往队列中加入一个空字符
		res.add("");
		for(int i=0;i<digits.length();i++) {
			//由当前遍历到的字符，取字典表中查找对应的字符串
			String letters = letter_map[digits.charAt(i)-'0'];
			int size = res.size();
			//计算出队列长度后，将队列中的每个元素挨个拿出来
			for(int j=0;j<size;j++) {
				//每次都从队列中拿出第一个元素
				String tmp = res.remove(0);
				//然后跟"def"这样的字符串拼接，并再次放到队列中
				for(int k=0;k<letters.length();k++) {
					res.add(tmp+letters.charAt(k));
				}
			}
		}
		return res;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/solution/tong-su-yi-dong-dong-hua-yan-shi-17-dian-hua-hao-m/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 85、[验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

![image-20210406083655806](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406083656.png)

### 思路

#### （1）递归

![image-20210406083859982](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406083900.png)

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    public boolean isValidBST(TreeNode node, long lower, long upper) {
        if (node == null) {
            return true;
        }
        if (node.val <= lower || node.val >= upper) {
            return false;
        }
        return isValidBST(node.left, lower, node.val) && isValidBST(node.right, node.val, upper);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/validate-binary-search-tree/solution/yan-zheng-er-cha-sou-suo-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210406090525252](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406090525.png)



#### （2）中序遍历

![image-20210406085756719](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406085756.png)

![fig1](https://assets.leetcode-cn.com/solution-static/98/98_fig1.gif)

```java
class Solution {
    long pre = Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        // 访问左子树
        if (!isValidBST(root.left)) {
            return false;
        }
        // 访问当前节点：如果当前节点小于等于中序遍历的前一个节点，说明不满足BST，返回 false；否则继续遍历。
        if (root.val <= pre) {
            return false;
        }
        pre = root.val;
        // 访问右子树
        return isValidBST(root.right);
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/validate-binary-search-tree/solution/zhong-xu-bian-li-qing-song-na-xia-bi-xu-miao-dong-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210406090408652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406090408.png)

###### 复杂度分析

![image-20210406090459833](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406090500.png)

## 86、[相同的树](https://leetcode-cn.com/problems/same-tree/)

![image-20210406091639712](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406091639.png)

### 思路

![image-20210406091748312](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406091748.png)

![image-20210406091804112](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406091804.png)

![image-20210406091829929](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406091830.png)

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        } else if (p == null || q == null) {
            return false;
        } else if (p.val != q.val) {
            return false;
        } else {
            return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/same-tree/solution/xiang-tong-de-shu-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 87、[从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

![image-20210406092859714](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210406092859.png)

### 思路

#### （1）递归

![image-20210407032106009](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407032107.png)

![image-20210407032124178](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407032124.png)

![image-20210407032335257](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407032335.png)

```java
class Solution {
    private Map<Integer, Integer> indexMap;

    public TreeNode myBuildTree(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        // preorder 为空，直接返回 null
        if (preorder_left > preorder_right) {
            return null;
        }

        // 前序遍历中的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = indexMap.get(preorder[preorder_root]);
        
        // 先把根节点建立出来
        TreeNode root = new TreeNode(preorder[preorder_root]);
        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;
        // 递归地构造左子树，并连接到根节点
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素，preorder_left左边界固定+1，当size_left_subtree=0时，就是左子树没有结点后，preorder_left>preorder_right,就是递归结束的时候了
        root.left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root.right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        // 构造哈希映射，帮助我们快速定位根节点
        indexMap = new HashMap<Integer, Integer>();
        for (int i = 0; i < n; i++) {
            indexMap.put(inorder[i], i);
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/cong-qian-xu-yu-zhong-xu-bian-li-xu-lie-gou-zao-9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210407033938294](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407033938.png)

###### 复杂度分析

![image-20210407040710164](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407040710.png)

#### （2）迭代

![image-20210407050737666](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407050737.png)

![image-20210407053118672](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407053119.png)

![image-20210407054340520](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210407054340.png)

![image-20210408021235161](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408021237.png)

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.length == 0) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[0]);
        Deque<TreeNode> stack = new LinkedList<TreeNode>();
        stack.push(root);
        int inorderIndex = 0;
        for (int i = 1; i < preorder.length; i++) {
            int preorderVal = preorder[i];
            TreeNode node = stack.peek();
            if (node.val != inorder[inorderIndex]) {
                node.left = new TreeNode(preorderVal);
                stack.push(node.left);
            } else {
                while (!stack.isEmpty() && stack.peek().val == inorder[inorderIndex]) {
                    node = stack.pop();
                    inorderIndex++;
                }
                node.right = new TreeNode(preorderVal);
                stack.push(node.right);
            }
        }
        return root;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/cong-qian-xu-yu-zhong-xu-bian-li-xu-lie-gou-zao-9/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 88、[从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

![image-20210408034542988](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408034544.png)

### 思路

#### （1）递归

![image-20210408034700589](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408034700.png)

![image-20210408035109066](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408035109.png)

![image-20210408035352970](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408035353.png)

![image-20210408035630563](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408035630.png)

###### 复杂度分析

![image-20210408035924156](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210408035924.png)

## 89、[路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

![image-20210409061112096](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409061113.png)

### 思路

#### （1）递归

![image-20210409061541125](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409061541.png)

![image-20210409061746873](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409061747.png)

![image-20210409062244305](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409062244.png)

```java
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    List<List<Integer>> result = new ArrayList<>();
    dfs(root, sum, new ArrayList<>(), result);
    return result;
}

public void dfs(TreeNode root, int sum, List<Integer> list,
                List<List<Integer>> result) {
    //如果节点为空直接返回
    if (root == null)
        return;
    //因为list是引用传递，为了防止递归的时候分支污染，我们要在每个路径
    //中都要新建一个subList
    List<Integer> subList = new ArrayList<>(list);
    //把当前节点值加入到subList中
    subList.add(new Integer(root.val));
    //如果到达叶子节点，就不能往下走了，直接return
    if (root.left == null && root.right == null) {
        //如果到达叶子节点，并且sum等于叶子节点的值，说明我们找到了一组，
        //要把它放到result中
        if (sum == root.val)
            result.add(subList);
        //到叶子节点之后直接返回，因为在往下就走不动了
        return;
    }
    //如果没到达叶子节点，就继续从他的左右两个子节点往下找，注意到
    //下一步的时候，sum值要减去当前节点的值
    dfs(root.left, sum - root.val, subList, result);
    dfs(root.right, sum - root.val, subList, result);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/path-sum-ii/solution/3chong-fang-shi-jie-jue-2chong-ji-bai-liao-100de-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）回溯，往下减

<img src="https://pic.leetcode-cn.com/1602838548-qDERwW-112.%E8%B7%AF%E5%BE%84%E6%80%BB%E5%92%8C.png" alt="112.路径总和.png" style="zoom:67%;" />

![image-20210409065343926](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409065344.png)

```java
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    List<List<Integer>> result = new ArrayList<>();
    dfs(root, sum, new ArrayList<>(), result);
    return result;
}

public void dfs(TreeNode root, int sum, List<Integer> list,
                List<List<Integer>> result) {
    //如果节点为空直接返回
    if (root == null)
        return;
    //把当前节点值加入到list中
    list.add(new Integer(root.val));
    //如果到达叶子节点，就不能往下走了，直接return
    if (root.left == null && root.right == null) {
        //如果到达叶子节点，并且sum等于叶子节点的值，说明我们找到了一组，
        //要把它放到result中
        if (sum == root.val)
            result.add(new ArrayList(list));
        //注意别忘了把最后加入的结点值给移除掉，因为下一步直接return了，
        //不会再走最后一行的remove了，所以这里在rerurn之前提前把最后
        //一个结点的值给remove掉。
        list.remove(list.size() - 1);
        //到叶子节点之后直接返回，因为在往下就走不动了
        return;
    }
    //如果没到达叶子节点，就继续从他的左右两个子节点往下找，注意到
    //下一步的时候，sum值要减去当前节点的值
    dfs(root.left, sum - root.val, list, result);
    dfs(root.right, sum - root.val, list, result);
    //我们要理解递归的本质，当递归往下传递的时候他最后还是会往回走，
    //我们把这个值使用完之后还要把它给移除，这就是回溯
    list.remove(list.size() - 1);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/path-sum-ii/solution/3chong-fang-shi-jie-jue-2chong-ji-bai-liao-100de-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）回溯，往下累加

![image-20210409070008762](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409070008.png)

#### （4）BFS

![image-20210409070933726](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409070934.png)

![image-20210409071145168](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409071145.png)

```java
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<>();
        //如果节点为空直接返回
        if (root == null)
            return res;
        //使用两个队列，一个存储结点，一个存储从更结点到当前节点的路径
        Queue<TreeNode> queueNode = new LinkedList<>();
        Queue<List<Integer>> queueList = new LinkedList<>();
        //根节点入队
        queueNode.add(root);
        //根节点的路径入队
        List<Integer> list = new ArrayList<>();
        list.add(root.val);
        queueList.add(list);

        while (!queueNode.isEmpty()) {
            //当前节点出队
            TreeNode node = queueNode.poll();
            //当前节点的路径出队
            List<Integer> tempList = queueList.poll();
            if (node.left == null && node.right == null && node.val == sum) {
                //如果满足条件，就把路径存储到res中
                res.add(tempList);
            }
            //左子节点不为空，左子节点和路径入队
            if (node.left != null) {
                tempList.add(node.left.val);
                queueList.add(new ArrayList<>(tempList));
                node.left.val += node.val;
                queueNode.add(node.left);
                tempList.remove(tempList.size() - 1);
            }
            //右子节点不为空，右子节点和路径入队
            if (node.right != null) {
                tempList.add(node.right.val);
                queueList.add(new ArrayList<>(tempList));
                node.right.val += node.val;
                queueNode.add(node.right);
            }
        }
        return res;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/path-sum-ii/solution/3chong-fang-shi-jie-jue-2chong-ji-bai-liao-100de-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210409071210106](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409071210.png)

#### （5）深度优先搜索非递归解决

![image-20210409071343105](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409071343.png)

```java
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<>();
        //如果节点为空直接返回
        if (root == null)
            return res;
        Stack<TreeNode> stackNode = new Stack<>();
        Stack<List<Integer>> stackList = new Stack<>();
        stackNode.add(root);

        List<Integer> list = new ArrayList<>();
        list.add(root.val);
        stackList.add(list);
        while (!stackNode.empty()) {
            TreeNode node = stackNode.pop();
            List<Integer> tempList = stackList.pop();
            if (node.left == null && node.right == null && node.val == sum) {
                //如果满足条件，就把路径存储到res中
                res.add(tempList);
            }

            if (node.right != null) {
                tempList.add(node.right.val);
                stackList.add(new ArrayList<>(tempList));
                node.right.val += node.val;
                stackNode.push(node.right);
                tempList.remove(tempList.size() - 1);

            }
            if (node.left != null) {
                tempList.add(node.left.val);
                stackList.add(new ArrayList<>(tempList));
                node.left.val += node.val;
                stackNode.push(node.left);
                tempList.remove(tempList.size() - 1);
            }
        }
        return res;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/path-sum-ii/solution/3chong-fang-shi-jie-jue-2chong-ji-bai-liao-100de-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210409071415371](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210409071415.png)



## 90、[二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

![image-20210410153729282](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410153730.png)

### 思路

#### （1）递归

> 题目中，要求说是 in-place，之前一直以为这个意思就是要求空间复杂度是 O(1)O(1)。偶然看见评论区大神的解释， in-place 的意思可能更多说的是直接在原来的节点上改变指向，空间复杂度并没有要求。所以这道题也可以用递归解一下。



![image-20210410153837099](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410153837.png)

![image-20210410153951096](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410153951.png)

```java
class Solution {
    public void flatten(TreeNode root) {
        if(root == null){
            return ;
        }
        //将根节点的左子树变成链表
        flatten(root.left);
        //将根节点的右子树变成链表
        flatten(root.right);
        TreeNode temp = root.right;
        //把树的右边换成左边的链表
        root.right = root.left;
        //记得要将左边置空
        root.left = null;
        //找到树的最右边的节点
        while(root.right != null) root = root.right;
        //把右边的链表接到刚才树的最右边的节点
        root.right = temp;
    }
}

作者：Geralt_U
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/114-er-cha-shu-zhan-kai-wei-lian-biao-by-ming-zhi-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410154051910](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410154052.png)

#### （2）迭代

> 可以发现展开的顺序其实就是二叉树的先序遍历。算法和 94 题中序遍历的 Morris 算法有些神似，我们需要两步完成这道题。
>
> 将左子树插入到右子树的地方
> 将原来的右子树接到左子树的最右边节点
> 考虑新的右子树的根节点，一直重复上边的过程，直到新的右子树为 null
> 可以看图理解下这个过程。

```java
    1
   / \
  2   5
 / \   \
3   4   6

//将 1 的左子树插入到右子树的地方
    1
     \
      2         5
     / \         \
    3   4         6        
//将原来的右子树接到左子树的最右边节点
    1
     \
      2          
     / \          
    3   4  
         \
          5
           \
            6
            
 //将 2 的左子树插入到右子树的地方，这个已经是迭代的第二个循环了
    1
     \
      2          
       \          
        3       4  
                 \
                  5
                   \
                    6   
        
 //将原来的右子树接到左子树的最右边节点
    1
     \
      2          
       \          
        3      
         \
          4  
           \
            5
             \
              6         
  
  ......

作者：windliang
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--26/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410155822606](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410155822.png)

```java
public void flatten(TreeNode root) {
    while (root != null) { 
        //左子树为 null，直接考虑下一个节点
        if (root.left == null) {
            root = root.right;
        } else {
            // 找左子树最右边的节点
            TreeNode pre = root.left;
            while (pre.right != null) {
                pre = pre.right;
            } 
            //将原来的右子树接到左子树的最右边节点
            pre.right = root.right;
            // 将左子树插入到右子树的地方
            root.right = root.left;
            root.left = null;
            // 考虑下一个节点
            root = root.right;
        }
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--26/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410155428316](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410155428.png)

#### （3）前序遍历+队列

![image-20210410160927667](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410160927.png)

![image-20210410161204889](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410161205.png)



```java
class Solution {
	public void flatten(TreeNode root) {
		if(root==null) {
			return;
		}
		LinkedList<TreeNode> res = new LinkedList<TreeNode>();
		//前序遍历整棵二叉树
		dfs(root,res);
		TreeNode head = res.removeFirst();
		head.left = null;
		//遍历链表，将链表中的TreeNode节点前后串联起来
		while(res.size()>0) {
			TreeNode tmp = res.removeFirst();			
			tmp.left = null;
			head.right = tmp;
			head = head.right;
		}	
	}
	
	//前序遍历整棵二叉树，并将遍历的结果放到数组中
	void dfs(TreeNode root, List<TreeNode> res) {
		if(root==null) {
			return;
		}
		res.add(root);
		dfs(root.left,res);
		dfs(root.right,res);
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/dong-hua-yan-shi-si-chong-jie-fa-114-er-cha-shu-zh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410161700256](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410161700.png)

#### （4）栈+BFS

![image-20210410162426542](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417230032.png)

![2.gif](https://pic.leetcode-cn.com/e118df68f78055170590bf41deb9188500a35b89ba6761ae23aedf92b7d82531-2.gif)

###### 复杂度分析

**时间复杂度:O(N)**
**空间复杂度:O(N)**

```java
class Solution {
	public void flatten(TreeNode root) {
		if(root==null) {
			return;
		}
		Stack<TreeNode> stack = new Stack<TreeNode>();
		stack.add(root);
		TreeNode pre = new TreeNode(-1);
		while(stack.size()>0) {
			//用栈作为辅助数据结构，从栈中弹出元素后
			//先将右节点放到栈中，再将左节点放入栈中，模拟前序遍历
			TreeNode tmp = stack.pop();
			pre.left = null;
			pre.right = tmp;
			//先放入右节点，再放入左边点，顺序不能反了
			if(tmp.right!=null) {
				stack.add(tmp.right);
			}
			if(tmp.left!=null) {
				stack.add(tmp.left);
			}
			pre = pre.right;
		}
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/dong-hua-yan-shi-si-chong-jie-fa-114-er-cha-shu-zh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410162753073](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410162753.png)



![image-20210410163257472](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410163257.png)

#### （5）后序遍历递归

![image-20210410170421898](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410170422.png)

![3.gif](https://pic.leetcode-cn.com/40b1d7aa9f21276443574bc065d80ad6a6e64cfe2fa733277c1609ec2d58ad5d-3.gif)

```java
class Solution {
	public void flatten(TreeNode root) {
		helper(root);
	}
	
	void helper(TreeNode root) {
		if(root==null) {
			return;
		}
		helper(root.left);
		helper(root.right);
		//将右子树挂到 左子树的最右边
		//再将整个左子树挂到根节点的右边
		if(root.left!=null) {
			TreeNode pre = root.left;
			while(pre.right!=null) {
				pre = pre.right;
			}
			pre.right = root.right;
			root.right = root.left;
			root.left = null;
		}
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/dong-hua-yan-shi-si-chong-jie-fa-114-er-cha-shu-zh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410170554362](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410170554.png)



![image-20210410171148374](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410171148.png)

#### （6）反向前序遍历递归

![image-20210410171706972](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410171707.png)

![4.gif](https://pic.leetcode-cn.com/6e5a4b61e7220a50fb3f4cdf63fdc6848b216d7b8d97a8e296cd2d23a0e8415d-4.gif)

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410172319.png)

```java
class Solution {
	public void flatten(TreeNode root) {
		helper(root);
	}
	
	TreeNode pre = null;
	void helper(TreeNode root) {
		if(root==null) {
			return;
		}
		//右节点-左节点-根节点 这种顺序正好跟前序遍历相反
		//用pre节点作为媒介，将遍历到的节点前后串联起来
		helper(root.right);
		helper(root.left);
		root.left = null;
		root.right = pre;
		pre = root;
	}
}


作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/dong-hua-yan-shi-si-chong-jie-fa-114-er-cha-shu-zh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

时间复杂度:O(N)
空间复杂度:O(h),h是树高度

![image-20210410172433436](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410172433.png)

## 91、[有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

![image-20210410172727941](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410172728.png)

### 思路

#### （1）快慢指针

![image-20210410232648226](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410232649.png)

![image-20210410233058344](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410233058.png)

```java
    public TreeNode sortedListToBST(ListNode head) {
        //边界条件的判断
        if (head == null)
            return null;
        if (head.next == null)
            return new TreeNode(head.val);
        //这里通过快慢指针找到链表的中间结点slow，pre就是中间
        //结点slow的前一个结点
        ListNode slow = head, fast = head, pre = null;
        while (fast != null && fast.next != null) {
            pre = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        //链表断开为两部分，一部分是node的左子节点，一部分是node
        //的右子节点
        pre.next = null;
        //node就是当前节点
        TreeNode node = new TreeNode(slow.val);
        //从head节点到pre节点是node左子树的节点
        node.left = sortedListToBST(head);
        //从slow.next到链表的末尾是node的右子树的结点
        node.right = sortedListToBST(slow.next);
        return node;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/solution/kuai-man-zhi-zhen-jie-jue-ji-bai-liao-100de-yong-h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410233621034](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410233621.png)

###### 复杂度分析

![image-20210411000141999](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411000142.png)

#### （2）list+递归

实际上还可以把链表中的值全都存储到集合list中，每次把list分为两部分，和上面原理一样

```java
    public TreeNode sortedListToBST(ListNode head) {
        List<Integer> list = new ArrayList<>();
        //把链表节点值全部提取到list中
        while (head != null) {
            list.add(head.val);
            head = head.next;
        }
        return sortedListToBSTHelper(list, 0, list.size() - 1);

    }

    TreeNode sortedListToBSTHelper(List<Integer> list, int left, int right) {
        if (left > right)
            return null;
        //把list中数据分为两部分
        int mid = left + (right - left) / 2;
        TreeNode root = new TreeNode(list.get(mid));
        root.left = sortedListToBSTHelper(list, left, mid - 1);
        root.right = sortedListToBSTHelper(list, mid + 1, right);
        return root;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/solution/kuai-man-zhi-zhen-jie-jue-ji-bai-liao-100de-yong-h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210410235335746](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410235336.png)

![image-20210410235358948](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210410235359.png)



## 92、[二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

![image-20210411021222180](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411021223.png)

![image-20210411021233853](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411021234.png)

### 思路

#### （1）迭代

![image-20210411100754307](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411100754.png)

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        TreeNode node = new TreeNode(val);
        if (root == null) {
            return node;
        }

        TreeNode cur = root;
        while (true) {
            if (cur.val > val) {
                if (cur.left == null) {
                    cur.left = node;
                    break;
                }
                cur = cur.left;
            } else {
                if (cur.right == null) {
                    cur.right = node;
                    break;
                } 
                cur = cur.right;
            }
        }
        return root;
    }
}


作者：sweetiee
链接：https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/solution/2-de-cha-ru-by-sweetiee/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210411100833592](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411100833.png)

#### （2）递归解法

![image-20210411101348713](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411101349.png)

###### 复杂度分析

![image-20210411101406451](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411101406.png)

## 93、[删除二叉搜索树中的节点](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

![image-20210411103334545](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411103334.png)

### 思路

##### 二叉搜索树三个性质

###### 1、中序遍历递增

![image-20210411105148882](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411105149.png)

###### 2、后继节点

![image-20210411105301930](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411105302.png)

###### 3、前驱节点

![image-20210411105448109](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411105448.png)

#### （1）递归1

![image-20210411110223055](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411110223.png)

![image-20210411110357505](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411110357.png)

![image-20210411112146855](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411112147.png)

![image-20210411115448061](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411115448.png)



```java
class Solution {
  /*
  One step right and then always left
  */
  public int successor(TreeNode root) {
    root = root.right;
    while (root.left != null) root = root.left;
    return root.val;
  }

  /*
  One step left and then always right
  */
  public int predecessor(TreeNode root) {
    root = root.left;
    while (root.right != null) root = root.right;
    return root.val;
  }

  public TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) return null;

    // delete from the right subtree
    if (key > root.val) root.right = deleteNode(root.right, key);
    // delete from the left subtree
    else if (key < root.val) root.left = deleteNode(root.left, key);
    // delete the current node
    else {
      // the node is a leaf，root=null后直接返回给上级作为删除的结果
      if (root.left == null && root.right == null) root = null;
      // the node is not a leaf and has a right child
      else if (root.right != null) {
        root.val = successor(root);
        //这里删除的返回结果直接作为当前节点的右子树
        root.right = deleteNode(root.right, root.val);
      }
      // the node is not a leaf, has no right child, and has a left child    
      else {
        root.val = predecessor(root);
        root.left = deleteNode(root.left, root.val);
      }
    }
    return root;
  }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/delete-node-in-a-bst/solution/shan-chu-er-cha-sou-suo-shu-zhong-de-jie-dian-by-l/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210411115541135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411115541.png)

#### （2）递归2

![image-20210411104353761](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411104354.png)

![image-20210411104409995](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411104410.png)

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null){
            return null;
        }
        //当前节点值比key小，则需要删除当前节点的左子树中key对应的值，并保证二叉搜索树的性质不变
        if(key < root.val){
            root.left = deleteNode(root.left,key);
        }
        //当前节点值比key大，则需要删除当前节点的右子树中key对应的值，并保证二叉搜索树的性质不变
        else if(key > root.val){
            root.right = deleteNode(root.right,key);
        }
        //当前节点等于key，则需要删除当前节点，并保证二叉搜索树的性质不变
        else{
            //当前节点没有左子树
            if(root.left == null){
                return root.right;
            }
            //当前节点没有右子树
            else if(root.right == null){
                return root.left;
            }
            //当前节点既有左子树又有右子树
            else{
                TreeNode node = root.right;
                //找到当前节点右子树最左边的叶子结点
                while(node.left != null){
                    node = node.left;
                }
                //将root的左子树放到root的右子树的最下面的左叶子节点的左子树上
                node.left = root.left;
                return root.right;
            }
        }
        return root;
    }
}

作者：Geralt_U
链接：https://leetcode-cn.com/problems/delete-node-in-a-bst/solution/450-shan-chu-er-cha-sou-suo-shu-zhong-de-jie-dia-6/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 94、[ 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)



![image-20210411150728501](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411150729.png)

### 思路

#### （1）递归

![image-20210411151042441](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411151043.png)

```java
    public int minDepth(TreeNode root) {
        if (root == null)
            return 0;
        //左子树的最小深度
        int left = minDepth(root.left);
        //右子树的最小深度
        int right = minDepth(root.right);
        //如果left和right都为0，我们返回1即可，
        //如果left和right只有一个为0，说明他只有一个子结点，我们只需要返回它另一个子节点的最小深度+1即可。
        //如果left和right都不为0，说明他有两个子节点，我们只需要返回最小深度的+1即可。
        return (left == 0 || right == 0) ? left + right + 1 : Math.min(left, right) + 1;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/solution/java-di-gui-he-fei-di-gui-liang-chong-fang-shi-de-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210411151338477](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411151338.png)

#### （2）非递归（层序BFS）

![image-20210411152713635](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411152714.png)

```java
    public int minDepth(TreeNode root) {
        if (root == null)
            return 0;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);//入队
        int level = 0;
        while (!queue.isEmpty()) {//队列不为空就继续循环
            level++;
            //这里的数量一定要提前算，固定下来，不能放到j<queue.size();因为queue的值在循环过程             //中是一直在变化的
            int levelCount = queue.size();
            for (int j = 0; j < levelCount; j++) {
                TreeNode node = queue.poll();//出队
                //如果当前node节点的左右子树都为空，直接返回level即可
                if (node.left == null && node.right == null)
                    return level;
                //左右子节点，哪个不为空，哪个加入到队列中
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
            }
        }
        return -1;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/solution/java-di-gui-he-fei-di-gui-liang-chong-fang-shi-de-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210411153011983](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411153012.png)

## 95、[求根节点到叶节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

![image-20210411160803898](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411160804.png)

### 思路

#### （1）递归

![image-20210411161611264](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411161611.png)

![image-20210411161824335](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411161824.png)

###### 复杂度分析

![image-20210411161845842](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411161846.png)

#### （2）BFS解决

![image-20210411162148756](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411162149.png)

```java
public int sumNumbers(TreeNode root) {
    //边界条件的判断
    if (root == null)
        return 0;
    Queue<TreeNode> nodeQueue = new LinkedList<>();
    Queue<Integer> valueQueue = new LinkedList<>();
    int res = 0;
    nodeQueue.add(root);
    valueQueue.add(root.val);
    while (!nodeQueue.isEmpty()) {
        //节点和节点对应的值同时出队
        TreeNode node = nodeQueue.poll();
        int value = valueQueue.poll();
        if (node.left == null && node.right == null) {
            //如果当前节点是叶子结点，说明找到了一条路径，把这条
            //路径的值加入到全局变量res中
            res += value;
        } else {
            //如果不是叶子节点就执行下面的操作
            if (node.left != null) {
                //把子节点和子节点的值分别加入到队列中，这里子节点的值
                //就是父节点的值*10+当前节点的值
                nodeQueue.add(node.left);
                valueQueue.add(value * 10 + node.left.val);
            }
            if (node.right != null) {
                nodeQueue.add(node.right);
                valueQueue.add(value * 10 + node.right.val);
            }
        }
    }
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/solution/dfshe-bfsliang-chong-fang-shi-jie-jue-zui-hao-de-j/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 96、[路径总和](https://leetcode-cn.com/problems/path-sum/)

![image-20210411165025574](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411165026.png)

### 思路

#### （1）递归

![image-20210411165343101](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411165343.png)

![image-20210411165505055](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411165505.png)

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        if (root.left == null && root.right == null) {
            return sum == root.val;
        }
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/path-sum/solution/lu-jing-zong-he-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210411165536818](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411165537.png)

![image-20210411165758161](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210411165758.png)

#### （2）BFS

![image-20210412035002364](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412035003.png)

![image-20210412035050537](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412035050.png)

![image-20210412035338252](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412035338.png)

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        Queue<TreeNode> queNode = new LinkedList<TreeNode>();
        Queue<Integer> queVal = new LinkedList<Integer>();
        queNode.offer(root);
        queVal.offer(root.val);
        while (!queNode.isEmpty()) {
            TreeNode now = queNode.poll();
            int temp = queVal.poll();
            if (now.left == null && now.right == null) {
                if (temp == sum) {
                    return true;
                }
                continue;
            }
            if (now.left != null) {
                queNode.offer(now.left);
                queVal.offer(now.left.val + temp);
            }
            if (now.right != null) {
                queNode.offer(now.right);
                queVal.offer(now.right.val + temp);
            }
        }
        return false;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/path-sum/solution/lu-jing-zong-he-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210412035825751](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412035825.png)

###### 复杂度分析

![image-20210412035853551](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412035853.png)

## 97、[二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

![image-20210412040703254](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412040703.png)

### 思路

#### （1）递归

![image-20210412040747777](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412040747.png)

```java
class Solution {
  public TreeNode searchBST(TreeNode root, int val) {
    if (root == null || val == root.val) return root;

    return val < root.val ? searchBST(root.left, val) : searchBST(root.right, val);
  }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/search-in-a-binary-search-tree/solution/er-cha-sou-suo-shu-zhong-de-sou-suo-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210412040825813](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412040825.png)

#### （2）迭代

![image-20210412040927567](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412040927.png)

```java
class Solution {
  public TreeNode searchBST(TreeNode root, int val) {
    while (root != null && val != root.val)
      root = val < root.val ? root.left : root.right;
    return root;
  }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/search-in-a-binary-search-tree/solution/er-cha-sou-suo-shu-zhong-de-sou-suo-by-leetcode/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### **复杂度分析**

![image-20210412040952814](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412040953.png)

![image-20210412041112633](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412041112.png)



## 98、[ 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

![image-20210412041938096](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412041938.png)



### 思路

#### （1）递归

![image-20210412042014528](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412042014.png)

![img](https://pic.leetcode-cn.com/b4b8a2f8461c581aa751bc0a0226b080334f957a6ef003abed463db97cc50990-file_1576126672590)

![img](https://pic.leetcode-cn.com/9409cecbc913c637353a6ec0ad97502c9d2e326822dec3e6a251530f02b8871b-file_1576126672596)

#### （2）栈 DFS

```java
class MyTreeNode {
    TreeNode root;
    int start;
    int end;

    MyTreeNode(TreeNode r, int s, int e) {
        this.root = r;
        this.start = s;
        this.end = e;
    }
}
public TreeNode sortedArrayToBST(int[] nums) {
    if (nums.length == 0) {
        return null;
    }
    Stack<MyTreeNode> rootStack = new Stack<>();
    int start = 0;
    int end = nums.length;
    int mid = (start + end) >>> 1;
    TreeNode root = new TreeNode(nums[mid]);
    TreeNode curRoot = root;
    rootStack.push(new MyTreeNode(root, start, end));
    while (end - start > 1 || !rootStack.isEmpty()) {
        //考虑左子树
        while (end - start > 1) {
            mid = (start + end) >>> 1; //当前根节点
            end = mid;//左子树的结尾
            mid = (start + end) >>> 1;//左子树的中点
            curRoot.left = new TreeNode(nums[mid]);
            curRoot = curRoot.left;
            rootStack.push(new MyTreeNode(curRoot, start, end));
        }
        //出栈考虑右子树
        MyTreeNode myNode = rootStack.pop();
        //当前作为根节点的 start end 以及 mid
        start = myNode.start;
        end = myNode.end;
        mid = (start + end) >>> 1;
        start = mid + 1; //右子树的 start
        curRoot = myNode.root; //当前根节点
        if (start < end) { //判断当前范围内是否有数
            mid = (start + end) >>> 1; //右子树的 mid
            curRoot.right = new TreeNode(nums[mid]);
            curRoot = curRoot.right;
            rootStack.push(new MyTreeNode(curRoot, start, end));
        }

    }

    return root;
}

作者：windliang
链接：https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-24/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）队列 BFS

```java
class MyTreeNode {
    TreeNode root;
    int start;
    int end;

    MyTreeNode(TreeNode r, int s, int e) {
        this.root = r;
        this.start = s;
        this.end = e;
    }
}
public TreeNode sortedArrayToBST3(int[] nums) {
    if (nums.length == 0) {
        return null;
    }
    Queue<MyTreeNode> rootQueue = new LinkedList<>();
    TreeNode root = new TreeNode(0);
    rootQueue.offer(new MyTreeNode(root, 0, nums.length));
    while (!rootQueue.isEmpty()) {
        MyTreeNode myRoot = rootQueue.poll();
        int start = myRoot.start;
        int end = myRoot.end;
        int mid = (start + end) >>> 1;
        TreeNode curRoot = myRoot.root;
        curRoot.val = nums[mid];
        if (start < mid) {
            curRoot.left = new TreeNode(0);
            rootQueue.offer(new MyTreeNode(curRoot.left, start, mid));
        }
        if (mid + 1 < end) {
            curRoot.right = new TreeNode(0);
            rootQueue.offer(new MyTreeNode(curRoot.right, mid + 1, end));
        }
    }

    return root;
}

作者：windliang
链接：https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-24/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 99、[填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

![image-20210412044140591](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412044140.png)

### 思路

#### （1）递归

![image-20210412044440733](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412044440.png)

```java
// package com.leetcode.explore.learnCard.dataStructureBinaryTree.conclusion4;

/**
 * 补充节点的右侧指针，不是完美二叉树
 */
public class Solution {
    public Node connect(Node root) {
        if (root == null || (root.right == null && root.left == null)) {
            return root;
        }
        if (root.left != null && root.right != null) {
            root.left.next = root.right;
            root.right.next = getNextNoNullChild(root);
        }
        //这里不能用root.left!=null，否则上面的if走完了，又会走这里的if
        if (root.left == null) {
            root.right.next = getNextNoNullChild(root);
        }
        if (root.right == null) {
            root.left.next = getNextNoNullChild(root);
        }

        //这里要注意：先递归右子树，否则右子树根节点next关系没建立好，左子树到右子树子节点无法正确挂载
        root.right = connect(root.right);
        root.left = connect(root.left);

        return root;
    }

    /**
     * 一路向右找到有子节点的根节点
     */
    private static Node getNextNoNullChild(Node root) {
        while (root.next != null) {
            if (root.next.left != null) {
                return root.next.left;
            }
            if (root.next.right != null) {
                return root.next.right;
            }
            root = root.next;
        }
        return null;
    }
}


作者：lovXin
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/solution/di-gui-fa-jian-dan-yi-dong-ban-xin-shou-kan-by-lov/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210412045035114](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412045035.png)

#### （2）BFS

![image-20210412051329488](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412051329.png)

![image-20210412051438825](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412051439.png)

```java
    public Node connect(Node root) {
        if (root == null)
            return root;
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            //每一层的数量
            int levelCount = queue.size();
            //前一个节点,这里类似链表的头结点，所以在当前层for循环遍历之前创建，然后一直移动
            Node pre = null;
            for (int i = 0; i < levelCount; i++) {
                //出队
                Node node = queue.poll();
                //如果pre为空就表示node节点是这一行的第一个，
                //没有前一个节点指向他，否则就让前一个节点指向他
                if (pre != null) {
                    pre.next = node;
                }
                //然后再让当前节点成为前一个节点
                pre = node;
                //左右子节点如果不为空就入队
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
            }
        }
        return root;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/solution/bfsjie-jue-zui-hao-de-ji-bai-liao-100de-yong-hu-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）BFS优化

![image-20210412052601079](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412052601.png)

![image-20210412052610189](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412052610.png)

```java
    public Node connect(Node root) {
        if (root == null)
            return root;
        //cur我们可以把它看做是每一层的链表
        Node cur = root;
        while (cur != null) {
            //遍历当前层的时候，为了方便操作在下一
            //层前面添加一个哑结点（注意这里是访问
            //当前层的节点，然后把下一层的节点串起来）
            Node dummy = new Node(0);
            //pre表示访下一层节点的前一个节点
            Node pre = dummy;
            //然后开始遍历当前层的链表
            while (cur != null) {
                if (cur.left != null) {
                    //如果当前节点的左子节点不为空，就让pre节点
                    //的next指向他，也就是把它串起来，这样就设置了每个结点的next指针了
                    pre.next = cur.left;
                    //然后再更新pre
                    pre = pre.next;
                }
                //同理参照左子树
                if (cur.right != null) {
                    pre.next = cur.right;
                    pre = pre.next;
                }
                //继续访问这一行的下一个节点
                cur = cur.next;
            }
            //把下一层串联成一个链表之后，让他赋值给cur，dummy.next就是把链表的头指针给cur了
            //后续继续循环，直到cur为空为止
            cur = dummy.next;
        }
        return root;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/solution/bfsjie-jue-zui-hao-de-ji-bai-liao-100de-yong-hu-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210412052628940](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412052629.png)



## 回溯

## 100、[分割回文串](https://leetcode-cn.com/problems/palindrome-partitioning/)

![image-20210412054421681](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412054421.png)

### 思路

#### （1）回溯算法

```java
public String substring(int beginIndex, int endIndex)
beginIndex对应String数字中的开始位置，
endIndex对应String中的结束位置；
1、取得的字符串长度为：endIndex - beginIndex;
2、从beginIndex开始取，到endIndex结束，从0开始数，其中不包括endIndex位置的字符
```

![131.分割回文串.png](https://pic.leetcode-cn.com/1604547208-PrOzRx-131.%E5%88%86%E5%89%B2%E5%9B%9E%E6%96%87%E4%B8%B2.png)

![image-20210412082345675](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412082345.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<String>> partition(String s) {
        int len = s.length();
        List<List<String>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        // Stack 这个类 Java 的文档里推荐写成 Deque<Integer> stack = new ArrayDeque<Integer>();
        // 注意：只使用 stack 相关的接口
        Deque<String> stack = new ArrayDeque<>();
        char[] charArray = s.toCharArray();
        dfs(charArray, 0, len, stack, res);
        return res;
    }

    /**
     * @param charArray
     * @param index     起始字符的索引
     * @param len       字符串 s 的长度，可以设置为全局变量
     * @param path      记录从根结点到叶子结点的路径
     * @param res       记录所有的结果
     */
    private void dfs(char[] charArray, int index, int len, Deque<String> path, List<List<String>> res) {
        if (index == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = index; i < len; i++) {
            // 因为截取字符串是消耗性能的，因此，采用传子串下标的方式判断一个子串是否是回文子串
            if (!checkPalindrome(charArray, index, i)) {
                continue;
            }
            //String(byte[] char,int offset,int length);通过char数组，从offset开始，总共length长的字节构造字符串对象  i+1-index为构造的长度，算长度的话，要加上自身
            path.addLast(new String(charArray, index, i + 1 - index));
            dfs(charArray, i + 1, len, path, res);
            path.removeLast();
        }
    }

    /**
     * 这一步的时间复杂度是 O(N)，优化的解法是，先采用动态规划，把回文子串的结果记录在一个表格里
     *
     * @param charArray
     * @param left      子串的左边界，可以取到
     * @param right     子串的右边界，可以取到
     * @return
     */
    private boolean checkPalindrome(char[] charArray, int left, int right) {
        while (left < right) {
            if (charArray[left] != charArray[right]) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/palindrome-partitioning/solution/hui-su-you-hua-jia-liao-dong-tai-gui-hua-by-liweiw/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210412085111856](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412085112.png)

注意：aab第一次截取a后，剩下的ab，这个时候也要再分情况，一次截图一个字符还是两个字符

这个又是一个for循环，所以是递归

###### 复杂度分析

![image-20210412091053253](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412091053.png)

#### （2）动态规划判断是否是回文

![image-20210412095350367](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412095350.png)





```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<String>> partition(String s) {
        int len = s.length();
        List<List<String>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        char[] charArray = s.toCharArray();
        // 预处理
        // 状态：dp[i][j] 表示 s[i][j] 是否是回文
        boolean[][] dp = new boolean[len][len];
        // 状态转移方程：在 s[i] == s[j] 的时候，dp[i][j] 参考 dp[i + 1][j - 1]
        for (int right = 0; right < len; right++) {
            // 注意：left <= right 取等号表示 1 个字符的时候也需要判断
            for (int left = 0; left <= right; left++) {
                if (charArray[left] == charArray[right] && (right - left <= 2 || dp[left + 1][right - 1])) {
                    dp[left][right] = true;
                }
            }
        }

        Deque<String> stack = new ArrayDeque<>();
        dfs(s, 0, len, dp, stack, res);
        return res;
    }

    private void dfs(String s, int index, int len, boolean[][] dp, Deque<String> path, List<List<String>> res) {
        if (index == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = index; i < len; i++) {
            if (dp[index][i]) {
                path.addLast(s.substring(index, i + 1));
                dfs(s, i + 1, len, dp, path, res);
                path.removeLast();
            }
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/palindrome-partitioning/solution/hui-su-you-hua-jia-liao-dong-tai-gui-hua-by-liweiw/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210412091253122](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210412091253.png)

![image-20210413042528458](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413042529.png)



## 101、[组合总和](https://leetcode-cn.com/problems/combination-sum/)

![image-20210413043650636](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413043650.png)

### 思路

#### （1）回溯

 ![image-20210413045714380](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413045714.png)

![image-20210413045856330](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413045856.png)

![image-20210413050954423](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413050954.png)

![image-20210413051435378](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413051435.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        int len = candidates.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        Deque<Integer> path = new ArrayDeque<>();
        dfs(candidates, 0, len, target, path, res);
        return res;
    }

    /**
     * @param candidates 候选数组
     * @param begin      搜索起点
     * @param len        冗余变量，是 candidates 里的属性，可以不传
     * @param target     每减去一个元素，目标值变小
     * @param path       从根结点到叶子结点的路径，是一个栈
     * @param res        结果集列表
     */
    private void dfs(int[] candidates, int begin, int len, int target, Deque<Integer> path, List<List<Integer>> res) {
        // target 为负数和 0 的时候不再产生新的孩子结点
        if (target < 0) {
            return;
        }
        if (target == 0) {
            res.add(new ArrayList<>(path));
            return;
        }

        // 重点理解这里从 begin 开始搜索的语意
        for (int i = begin; i < len; i++) {
            path.addLast(candidates[i]);

            // 注意：由于每一个元素可以重复使用，下一轮搜索的起点依然是 i，这里非常容易弄错
            dfs(candidates, i, len, target - candidates[i], path, res);

            // 状态重置
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）剪枝提速

####  ![image-20210413052041266](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413052041.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        int len = candidates.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        // 排序是剪枝的前提
        Arrays.sort(candidates);
        Deque<Integer> path = new ArrayDeque<>();
        dfs(candidates, 0, len, target, path, res);
        return res;
    }

    private void dfs(int[] candidates, int begin, int len, int target, Deque<Integer> path, List<List<Integer>> res) {
        // 由于进入更深层的时候，小于 0 的部分被剪枝，因此递归终止条件值只判断等于 0 的情况
        if (target == 0) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = begin; i < len; i++) {
            // 重点理解这里剪枝，前提是候选数组已经有序，
            if (target - candidates[i] < 0) {
                break;
            }
            
            path.addLast(candidates[i]);
            dfs(candidates, i, len, target - candidates[i], path, res);
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 回溯算法打印结果

![image-20210413052248259](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413052248.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;


public class Solution {

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        int len = candidates.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        Deque<Integer> path = new ArrayDeque<>();
        dfs(candidates, 0, len, target, path, res);
        return res;
    }

    private void dfs(int[] candidates, int begin, int len, int target, Deque<Integer> path, List<List<Integer>> res) {
        if (target < 0) {
            return;
        }
        if (target == 0) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = begin; i < len; i++) {
            path.addLast(candidates[i]);
            System.out.println("递归之前 => " + path + "，剩余 = " + (target - candidates[i]));

            dfs(candidates, i, len, target - candidates[i], path, res);

            path.removeLast();
            System.out.println("递归之后 => " + path);

        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] candidates = new int[]{2, 3, 6, 7};
        int target = 7;
        List<List<Integer>> res = solution.combinationSum(candidates, target);
        System.out.println("输出 => " + res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

打印输出：

```java
递归之前 => [2]，剩余 = 5
递归之前 => [2, 2]，剩余 = 3
递归之前 => [2, 2, 2]，剩余 = 1
递归之前 => [2, 2, 2, 2]，剩余 = -1
递归之后 => [2, 2, 2]
递归之前 => [2, 2, 2, 3]，剩余 = -2
递归之后 => [2, 2, 2]
递归之前 => [2, 2, 2, 6]，剩余 = -5
递归之后 => [2, 2, 2]
递归之前 => [2, 2, 2, 7]，剩余 = -6
递归之后 => [2, 2, 2]
递归之后 => [2, 2]
递归之前 => [2, 2, 3]，剩余 = 0
递归之后 => [2, 2]
递归之前 => [2, 2, 6]，剩余 = -3
递归之后 => [2, 2]
递归之前 => [2, 2, 7]，剩余 = -4
递归之后 => [2, 2]
递归之后 => [2]
递归之前 => [2, 3]，剩余 = 2
递归之前 => [2, 3, 3]，剩余 = -1
递归之后 => [2, 3]
递归之前 => [2, 3, 6]，剩余 = -4
递归之后 => [2, 3]
递归之前 => [2, 3, 7]，剩余 = -5
递归之后 => [2, 3]
递归之后 => [2]
递归之前 => [2, 6]，剩余 = -1
递归之后 => [2]
递归之前 => [2, 7]，剩余 = -2
递归之后 => [2]
递归之后 => []
递归之前 => [3]，剩余 = 4
递归之前 => [3, 3]，剩余 = 1
递归之前 => [3, 3, 3]，剩余 = -2
递归之后 => [3, 3]
递归之前 => [3, 3, 6]，剩余 = -5
递归之后 => [3, 3]
递归之前 => [3, 3, 7]，剩余 = -6
递归之后 => [3, 3]
递归之后 => [3]
递归之前 => [3, 6]，剩余 = -2
递归之后 => [3]
递归之前 => [3, 7]，剩余 = -3
递归之后 => [3]
递归之后 => []
递归之前 => [6]，剩余 = 1
递归之前 => [6, 6]，剩余 = -5
递归之后 => [6]
递归之前 => [6, 7]，剩余 = -6
递归之后 => [6]
递归之后 => []
递归之前 => [7]，剩余 = 0
递归之后 => []
输出 => [[2, 2, 3], [7]]

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

<img src="https://pic.leetcode-cn.com/1598162219-BpwhKL-image.png" alt="image.png" style="zoom:67%;" />

针对参考代码 2 添加打印输出：

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        int len = candidates.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        Arrays.sort(candidates);
        Deque<Integer> path = new ArrayDeque<>();
        dfs(candidates, 0, len, target, path, res);
        return res;
    }

    private void dfs(int[] candidates, int begin, int len, int target, Deque<Integer> path, List<List<Integer>> res) {
        if (target == 0) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = begin; i < len; i++) {
            if (target - candidates[i] < 0) {
                break;
            }

            path.addLast(candidates[i]);
            System.out.println("递归之前 => " + path + "，剩余 = " + (target - candidates[i]));

            dfs(candidates, i, len, target - candidates[i], path, res);
            path.removeLast();
            System.out.println("递归之后 => " + path);
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] candidates = new int[]{2, 3, 6, 7};
        int target = 7;
        List<List<Integer>> res = solution.combinationSum(candidates, target);
        System.out.println("输出 => " + res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

打印输出：

![image-20210413052425958](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413052426.png)



## 102、[组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

![image-20210413061449308](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413061449.png)

### 思路

#### （1）回溯去重

![image-20210413062552245](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413062552.png)

![image-20210413063724029](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210413063724.png)

![Page3.jpg](https://pic.leetcode-cn.com/14ea588721288265fed982a4934afd4de595eb3c92f80b44b4a15759ff3b52df-Page3.jpg)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        int len = candidates.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        // 关键步骤
        Arrays.sort(candidates);

        Deque<Integer> path = new ArrayDeque<>(len);
        dfs(candidates, len, 0, target, path, res);
        return res;
    }

    /**
     * @param candidates 候选数组
     * @param len        冗余变量
     * @param begin      从候选数组的 begin 位置开始搜索
     * @param target     表示剩余，这个值一开始等于 target，基于题目中说明的"所有数字（包括目标数）都是正整数"这个条件
     * @param path       从根结点到叶子结点的路径
     * @param res
     */
    private void dfs(int[] candidates, int len, int begin, int target, Deque<Integer> path, List<List<Integer>> res) {
        if (target == 0) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = begin; i < len; i++) {
            // 大剪枝：减去 candidates[i] 小于 0，减去后面的 candidates[i + 1]、candidates[i + 2] 肯定也小于 0，因此用 break
            if (target - candidates[i] < 0) {
                break;
            }

            // 小剪枝：同一层相同数值的结点，从第 2 个开始，候选数更少，结果一定发生重复，因此跳过，用 continue
            if (i > begin && candidates[i] == candidates[i - 1]) {
                continue;
            }

            path.addLast(candidates[i]);
            // 调试语句 ①
            // System.out.println("递归之前 => " + path + "，剩余 = " + (target - candidates[i]));

            // 因为元素不可以重复使用，这里递归传递下去的是 i + 1 而不是 i
            dfs(candidates, len, i + 1, target - candidates[i], path, res);

            path.removeLast();
            // 调试语句 ②
            // System.out.println("递归之后 => " + path + "，剩余 = " + (target - candidates[i]));
        }
    }

    public static void main(String[] args) {
        int[] candidates = new int[]{10, 1, 2, 7, 6, 1, 5};
        int target = 8;
        Solution solution = new Solution();
        List<List<Integer>> res = solution.combinationSum2(candidates, target);
        System.out.println("输出 => " + res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum-ii/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 103、[组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)

![image-20210414020116284](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414020117.png)

### 思路

#### （1）回溯+剪枝

![image-20210414020848879](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414020849.png)

![image-20210414020902900](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414020903.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();

        // 根据官方对 Stack 的使用建议，这里将 Deque 对象当做 stack 使用
        // 注意只使用关于栈的接口
        Deque<Integer> path = new ArrayDeque<>();
        dfs(k, n, 1, path, res);
        return res;
    }

    /**
     * @param k       剩下要找 k 个数
     * @param residue 剩余多少
     * @param start   下一轮搜索的起始元素是多少
     * @param path    深度优先遍历的路径参数（状态变量）
     * @param res     保存结果集的列表
     */
    private void dfs(int k, int residue, int start, Deque<Integer> path, List<List<Integer>> res) {
        // 这一步判断可以放到上一层，减少递归深度
        if (residue < 0) {
            return;
        }

        if (k == 0) {
            if (residue == 0) {
                res.add(new ArrayList<>(path));
                return;
            }
            return;
        }

        for (int i = start; i <= 9; i++) {
            path.addLast(i);
            dfs(k - 1, residue - i, i + 1, path, res);
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum-iii/solution/hui-su-jian-zhi-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210414021047356](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414021047.png)

![image-20210414021306374](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414021306.png)

![image-20210414021833613](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414021833.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();

        // 一开始做一些特殊判断
        if (k <= 0 || n <= 0 || k > n) {
            return res;
        }

        // 寻找 n 的上限：[9, 8, ... , (9 - k + 1)]，它们的和为 (19 - k) * k / 2
        // 比上限还大，就不用搜索了：
        if (n > (19 - k) * k / 2) {
            return res;
        }

        // 根据官方对 Stack 的使用建议，这里将 Deque 对象当做 stack 使用
        // 注意只使用关于栈的接口
        Deque<Integer> path = new ArrayDeque<>();
        dfs(k, n, 1, path, res);
        return res;
    }

    /**
     * @param k       剩下要找 k 个数
     * @param residue 剩余多少
     * @param start   下一轮搜索的起始元素是多少
     * @param path    深度优先遍历的路径参数（状态变量）
     * @param res     保存结果集的列表
     */
    private void dfs(int k, int residue, int start, Deque<Integer> path, List<List<Integer>> res) {
        // 剪枝：[start, 9] 这个区间里的数都不够 k 个，不用继续往下搜索
        if (10 - start < k) {
            return;
        }
        if (k == 0) {
            if (residue == 0) {
                res.add(new ArrayList<>(path));
                return;
            }
        }

        // 枚举起点值 [..., 7, 8, 9]
        // 找 3 个数，起点最多到 7
        // 找 2 个数，起点最多到 8
        // 规律是，起点上界 + k = 10，故起点上界 = 10 - k
        for (int i = start; i <= 10 - k; i++) {

//            if ((2 * i + k - 1) * k / 2 > residue) {
//                break;
//            }

            // 剪枝
            if (residue - i < 0) {
                break;
            }
            path.addLast(i);
            dfs(k - 1, residue - i, i + 1, path, res);
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum-iii/solution/hui-su-jian-zhi-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 104、[组合总和 Ⅳ](https://leetcode-cn.com/problems/combination-sum-iv/)

![image-20210414023823688](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414023823.png)

### 思路

#### （1）背包问题动态规划

![image-20210414031437980](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414031438.png)

![image-20210414031557279](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414031557.png)

![image-20210414033124081](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414033124.png)

```java
public class Solution {

    /**
     * 这里状态定义就是题目要求的，并不难，状态转移方程要动点脑子，也不难：
     * 状态转移方程：dp[i]= dp[i - nums[0]] + dp[i - nums[1]] + dp[i - nums[2]] + ... （当 [] 里面的数 >= 0）
     * 特别注意：dp[0] = 1，表示，如果那个硬币的面值刚刚好等于需要凑出的价值，这个就成为 1 种组合方案
     * 再举一个具体的例子：nums=[1, 3, 4], target=7;
     * dp[7] = dp[6] + dp[4] + dp[3]
     * 即：7 的组合数可以由三部分组成，1 和 dp[6]，3 和 dp[4], 4 和dp[3];
     *
     * @param nums
     * @param target
     * @return
     */
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        // 这个值被其它状态参考，设置为 1 是合理的
        dp[0] = 1;

        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (num <= i) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combination-sum-iv/solution/dong-tai-gui-hua-python-dai-ma-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 105、[组合](https://leetcode-cn.com/problems/combinations/)

![image-20210414035726255](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414035726.png)

### 思路

#### （1）回溯剪枝

![image-20210414040459107](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414040459.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (k <= 0 || n < k) {
            return res;
        }
        // 从 1 开始是题目的设定
        Deque<Integer> path = new ArrayDeque<>();
        dfs(n, k, 1, path, res);
        return res;
    }

    private void dfs(int n, int k, int begin, Deque<Integer> path, List<List<Integer>> res) {
        // 递归终止条件是：path 的长度等于 k
        if (path.size() == k) {
            res.add(new ArrayList<>(path));
            return;
        }

        // 遍历可能的搜索起点
        for (int i = begin; i <= n; i++) {
            // 向路径变量里添加一个数
            path.addLast(i);
            // 下一轮搜索，设置的搜索起点要加 1，因为组合数理不允许出现重复的元素
            dfs(n, k, i + 1, path, res);
            // 重点理解这里：深度优先遍历有回头的过程，因此递归之前做了什么，递归之后需要做相同操作的逆向操作
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combinations/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-ma-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210414040532090](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414040532.png)

#### （2）优化

![image-20210414040611511](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414040611.png)

![img](https://pic.leetcode-cn.com/3ddd55697423b5831cbbd42f4b901ebbade0daa456c651a70c758fe359d8a0d1-image.png)

![image-20210414040846723](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414040846.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (k <= 0 || n < k) {
            return res;
        }
        Deque<Integer> path = new ArrayDeque<>();
        dfs(n, k, 1, path, res);
        return res;
    }

    private void dfs(int n, int k, int index, Deque<Integer> path, List<List<Integer>> res) {
        if (path.size() == k) {
            res.add(new ArrayList<>(path));
            return;
        }

        // 只有这里 i <= n - (k - path.size()) + 1 与参考代码 1 不同
        for (int i = index; i <= n - (k - path.size()) + 1; i++) {
            path.addLast(i);
            dfs(n, k, i + 1, path, res);
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combinations/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-ma-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210414040909582](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414040909.png)

#### （3）二叉树dfs参照位运算

![image-20210414042834718](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414042835.png)

![image-20210414043403982](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414043404.png)

![image-20210414043433211](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414043433.png)

即n=9,从中取3个元素，当begin>7后，就取不到符合条件的数组了



```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        if (k <= 0 || n < k) {
            return res;
        }

        // 为了防止底层动态数组扩容，初始化的时候传入最大长度
        Deque<Integer> path = new ArrayDeque<>(k);
        dfs(1, n, k, path, res);
        return res;
    }

    private void dfs(int begin, int n, int k, Deque<Integer> path, List<List<Integer>> res) {
        if (k == 0) {
            res.add(new ArrayList<>(path));
            return;
        }

        // 基础版本的递归终止条件：if (begin == n + 1) {
        if (begin > n - k + 1) {
            return;
        }
        // 不选当前考虑的数 begin，直接递归到下一层
        dfs(begin + 1, n, k, path, res);

        // 不选当前考虑的数 begin，递归到下一层的时候 k - 1，这里 k 表示还需要选多少个数
        path.addLast(begin);
        dfs(begin + 1, n, k - 1, path, res);
        // 深度优先遍历有回头的过程，因此需要撤销选择
        path.removeLast();
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/combinations/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-ma-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （4）递归解决

![image-20210414044019518](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210414044019.png)

```java
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        //边界条件判断
        if (n < k || k == 0)
            return res;
        //不选择第n个，从前面n-1个数字中选择k-1个构成一个集合
        res = combine(n - 1, k - 1);
        //如果res是空，添加一个空的集合
        if (res.isEmpty())
            res.add(new ArrayList<>());
        //然后在前面选择的集合res中的每个子集合的后面添加一个数字n
        for (List<Integer> list : res)
            list.add(n);
        //res中不光要包含选择第n个数字的集合，也要包含不选择第n
        //个数字的集合
        res.addAll(combine(n - 1, k));
        return res;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/combinations/solution/hui-su-jie-jue-tu-wen-xiang-jie-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 106、[全排列](https://leetcode-cn.com/problems/permutations/)

![image-20210415013300726](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415013301.png)

### 思路

#### （1）回溯

![image-20210415015422068](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415015422.png)

![image-20210415015436252](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415015436.png)

![image-20210415015454656](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415015454.png)

![image-20210415015503245](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415015503.png)

![image-20210415015521933](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415015522.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;


public class Solution {

    public List<List<Integer>> permute(int[] nums) {
        int len = nums.length;
        // 使用一个动态数组保存所有可能的全排列
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        boolean[] used = new boolean[len];
        Deque<Integer> path = new ArrayDeque<>(len);

        dfs(nums, len, 0, path, used, res);
        return res;
    }

    private void dfs(int[] nums, int len, int depth,
                     Deque<Integer> path, boolean[] used,
                     List<List<Integer>> res) {
        if (depth == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = 0; i < len; i++) {
            if (!used[i]) {
                path.addLast(nums[i]);
                used[i] = true;

                System.out.println("  递归之前 => " + path);
                dfs(nums, len, depth + 1, path, used, res);

                used[i] = false;
                path.removeLast();
                System.out.println("递归之后 => " + path);
            }
        }
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        Solution solution = new Solution();
        List<List<Integer>> lists = solution.permute(nums);
        System.out.println(lists);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liweiw/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210415021337230](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415021337.png)

![image-20210415022057955](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415022058.png)

用ArrayList效率貌似更好点

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {

        List<List<Integer>> res = new ArrayList<>();
        int[] visited = new int[nums.length];
        backtrack(res, nums, new ArrayList<Integer>(), visited);
        return res;

    }

    private void backtrack(List<List<Integer>> res, int[] nums, ArrayList<Integer> tmp, int[] visited) {
        if (tmp.size() == nums.length) {
            res.add(new ArrayList<>(tmp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (visited[i] == 1) continue;
            visited[i] = 1;
            tmp.add(nums[i]);
            backtrack(res, nums, tmp, visited);
            visited[i] = 0;
            tmp.remove(tmp.size() - 1);
        }
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-by-powcai-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210415022208299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415022208.png)



## 107、[全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

![image-20210415030905962](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415030906.png)

### 思路

#### （1）回溯

![image-20210415031116362](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415031116.png)

![image-20210415031433045](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415031433.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<List<Integer>> permuteUnique(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        // 排序（升序或者降序都可以），排序是剪枝的前提
        Arrays.sort(nums);

        boolean[] used = new boolean[len];
        // 使用 Deque 是 Java 官方 Stack 类的建议
        Deque<Integer> path = new ArrayDeque<>(len);
        dfs(nums, len, 0, used, path, res);
        return res;
    }

    private void dfs(int[] nums, int len, int depth, boolean[] used, Deque<Integer> path, List<List<Integer>> res) {
        if (depth == len) {
            res.add(new ArrayList<>(path));
            return;
        }

        for (int i = 0; i < len; ++i) {
            if (used[i]) {
                continue;
            }

            // 剪枝条件：i > 0 是为了保证 nums[i - 1] 有意义
            // 写 !used[i - 1] 是因为 nums[i - 1] 在深度优先遍历的过程中刚刚被撤销选择
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                continue;
            }

            path.addLast(nums[i]);
            used[i] = true;

            dfs(nums, len, depth + 1, used, path, res);
            // 回溯部分的代码，和 dfs 之前的代码是对称的
            used[i] = false;
            path.removeLast();
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/permutations-ii/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liwe-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210415031457680](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415031457.png)

![image-20210415033134280](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415033134.png)

###### user[i-1]==true也成立的原因

![image-20210415032407999](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415032408.png)

![image-20210415032733267](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415032733.png)

![image-20210415032751476](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415032751.png)

![image-20210415032815864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415032816.png)

![image-20210415032830216](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415032830.png)



## 108、[子集](https://leetcode-cn.com/problems/subsets/)

![image-20210415035230018](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415035230.png)

### 思路

#### （1）回溯搜索

方法一：回溯搜索

执行一次深度优先遍历，一条路走到底，走不通的时候，返回回来，继续执行，一直这样下去，直到回到起点。

###### **参考代码 1**：回溯中记录结点。

<img src="https://pic.leetcode-cn.com/50dd3b79894706fef7bf1fd2d184ac2c89639da07fad92c75ed5a679b1b1bc8d-0078-backtracking.gif" alt="0078-backtracking.gif" style="zoom:67%;" />

![image-20210415041603083](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415041603.png)

```java
import java.util.ArrayList;
import java.util.List;


public class Solution {

    private List<List<Integer>> res;

    private void find(int[] nums, int begin, List<Integer> pre) {
        // 没有显式的递归终止
        //这里只要每次有递归，就把结果加入到最终集合中，递归结束靠for循环的终止条件来终止
        res.add(new ArrayList<>(pre));// 注意：Java 的引用传递机制，这里要 new 一下
        for (int i = begin; i < nums.length; i++) {
            pre.add(nums[i]);
            //不能重复，所以每次递归都取下一位的值
            find(nums, i + 1, pre);
            pre.remove(pre.size() - 1);// 组合问题，状态在递归完成后要重置
        }
    }

    public List<List<Integer>> subsets(int[] nums) {
        int len = nums.length;
        res = new ArrayList<>();
        if (len == 0) {
            return res;
        }
        List<Integer> pre = new ArrayList<>();
        find(nums, 0, pre);
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/subsets/solution/hui-su-python-dai-ma-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210415042043487](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415042043.png)



###### **参考代码 2**：回溯中记录深度

![image.png](https://pic.leetcode-cn.com/596a16391607ec3505794d6ecb4d5d854f480440f025fc646c9f6225f83412e0-image.png)

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Solution {

    public List<List<Integer>> subsets(int[] nums) {
        int size = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (size == 0) {
            return res;
        }
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < size + 1; i++) {
            dfs(nums, 0, i, stack, res);
        }
        return res;
    }

    private void dfs(int[] nums, int start, int depth, Stack<Integer> path, List<List<Integer>> res) {
        if (depth == path.size()) {
            res.add(new ArrayList<>(path));
            return;
        }
        for (int i = start; i < nums.length; i++) {
            path.add(nums[i]);
            dfs(nums, i + 1, depth, path, res);
            path.pop();
        }
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        Solution solution = new Solution();
        List<List<Integer>> subsets = solution.subsets(nums);
        System.out.println(subsets);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/subsets/solution/hui-su-python-dai-ma-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **参考代码 3**：

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Solution {

    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int len = nums.length;
        if (len == 0) {
            return res;
        }
        Stack<Integer> stack = new Stack<>();
        dfs(nums, 0, len, stack, res);
        return res;
    }

    private void dfs(int[] nums, int index, int len,
                     Stack<Integer> stack, List<List<Integer>> res) {
        if (index == len) {
            res.add(new ArrayList<>(stack));
            return;
        }
        // 当前数可选，也可以不选

        // 不选，直接进入下一层
        dfs(nums, index + 1, len, stack, res);

        // 选了有，进入下一层
        stack.add(nums[index]);
        dfs(nums, index + 1, len, stack, res);
        stack.pop();
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        Solution solution = new Solution();
        List<List<Integer>> subsets = solution.subsets(nums);
        System.out.println(subsets);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/subsets/solution/hui-su-python-dai-ma-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **参考代码 4**：

![image-20210415042440420](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415042440.png)

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class Solution {

    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int len = nums.length;
        if (len == 0) {
            return res;
        }
        Stack<Integer> stack = new Stack<>();
        dfs(nums, 0, len, stack, res);
        return res;
    }

    private void dfs(int[] nums, int index, int len,
                     Stack<Integer> stack, List<List<Integer>> res) {
        if (index == len) {
            res.add(new ArrayList<>(stack));
            return;
        }
        // 当前数可选，也可以不选
        // 选了有，进入下一层
        stack.add(nums[index]);
        dfs(nums, index + 1, len, stack, res);
        stack.pop();

        // 不选，直接进入下一层
        dfs(nums, index + 1, len, stack, res);
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        Solution solution = new Solution();
        List<List<Integer>> subsets = solution.subsets(nums);
        System.out.println(subsets);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/subsets/solution/hui-su-python-dai-ma-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2） 位操作

![image-20210415045356764](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415045357.png)

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    int bit_nums = nums.length;
    int ans_nums = 1 << bit_nums; //执行 2 的 n 次方
    for (int i = 0; i < ans_nums; i++) {
        List<Integer> tmp = new ArrayList<>();
        int count = 0; //记录当前对应数组的哪一位
        int i_copy = i; //用来移位
        while (i_copy != 0) { 
            if ((i_copy & 1) == 1) { //判断当前位是否是 1
                tmp.add(nums[count]);
            }
            count++;
            i_copy = i_copy >> 1;//右移一位
        }
        ans.add(tmp);

    }
    return ans;
}

作者：windliang
链接：https://leetcode-cn.com/problems/subsets/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--10/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210415050010581](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415050010.png)

```java
public static List<List<Integer>> subsets(int[] nums) {
    //子集的长度是2的nums.length次方，这里通过移位计算
    int length = 1 << nums.length;
    List<List<Integer>> res = new ArrayList<>(length);
    //遍历从0到length中间的所有数字，根据数字中1的位置来找子集
    for (int i = 0; i < length; i++) {
        List<Integer> list = new ArrayList<>();
        for (int j = 0; j < nums.length; j++) {
            //如果数字i的某一个位置是1，就把数组中对
            //应的数字添加到集合
            if (((i >> j) & 1) == 1)
                list.add(nums[j]);
        }
        res.add(list);
    }
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/subsets/solution/hui-su-wei-yun-suan-di-gui-deng-gong-4chong-fang-s/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

<img src="https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415050032.png" alt="image-20210415050032592" />

<img src="https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415050032.png" alt="image-20210415050032592" style="zoom: 67%;" />![image.png](https://pic.leetcode-cn.com/1600562175-KhRyHn-image.png)

![image.png](https://pic.leetcode-cn.com/1600562175-KhRyHn-image.png)

![image.png](https://pic.leetcode-cn.com/1600562175-KhRyHn-image.png)

![image-20210415050511841](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415050512.png)

## 109、[子集 II](https://leetcode-cn.com/problems/subsets-ii/)

![image-20210415052444969](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415052445.png)

### 思路

#### （1）回溯

![image-20210416043913312](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416043915.png)

![image-20210415053429747](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210415053430.png)

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    Arrays.sort(nums); //排序
    getAns(nums, 0, new ArrayList<>(), ans);
    return ans;
}

private void getAns(int[] nums, int start, ArrayList<Integer> temp, List<List<Integer>> ans) {
    ans.add(new ArrayList<>(temp));
    for (int i = start; i < nums.length; i++) {
        //和上个数字相等就跳过
        if (i > start && nums[i] == nums[i - 1]) {
            continue;
        }
        temp.add(nums[i]);
        getAns(nums, i + 1, temp, ans);
        temp.remove(temp.size() - 1);
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/subsets-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-19/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）迭代法

![image-20210416044807789](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416044809.png)

![image-20210416045012283](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416045013.png)

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    ans.add(new ArrayList<>());// 初始化空数组
    Arrays.sort(nums);
    int start = 1; //保存新解的开始位置
    for (int i = 0; i < nums.length; i++) {
        List<List<Integer>> ans_tmp = new ArrayList<>();
        // 遍历之前的所有结果
        for (int j = 0; j < ans.size(); j++) {
            List<Integer> list = ans.get(j);
            //如果出现重复数字，就跳过所有旧解,小于新解下标的，表示跟重复的数字已经组合过了，都				//不考虑，跳过
            if (i > 0 && nums[i] == nums[i - 1] && j < start) {
                continue;
            }
            List<Integer> tmp = new ArrayList<>(list);
            tmp.add(nums[i]); // 加入新增数字
            ans_tmp.add(tmp);
        }

        start = ans.size(); //更新新解的开始位置
        ans.addAll(ans_tmp);
    }
    return ans;
}

作者：windliang
链接：https://leetcode-cn.com/problems/subsets-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-19/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 背包问题

## 110、[分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

![image-20210416053123258](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416053124.png)

### 思路

#### （1）动态规划

![image-20210416064837126](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416064841.png)

![image-20210416064946021](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416064946.png)

![image-20210416065230866](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416065231.png)

![image-20210416065250359](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416065250.png)

![image-20210416065327675](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416065328.png)

![image-20210416065721374](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416065722.png)

![image-20210416070007291](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416070007.png)

![image-20210416070032037](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416070032.png)

![img](https://assets.leetcode-cn.com/solution-static/416/7.png)

![img](https://assets.leetcode-cn.com/solution-static/416/8.png)

![img](https://assets.leetcode-cn.com/solution-static/416/9.png)

![img](https://assets.leetcode-cn.com/solution-static/416/10.png)

![img](https://assets.leetcode-cn.com/solution-static/416/11.png)

![img](https://assets.leetcode-cn.com/solution-static/416/12.png)

![image-20210416084943961](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416084945.png)

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n = nums.length;
        if (n < 2) {
            return false;
        }
        int sum = 0, maxNum = 0;
        for (int num : nums) {
            sum += num;
            maxNum = Math.max(maxNum, num);
        }
        if (sum % 2 != 0) {
            return false;
        }
        int target = sum / 2;
        if (maxNum > target) {
            return false;
        }
        boolean[][] dp = new boolean[n][target + 1];
        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }
        dp[0][nums[0]] = true;
        for (int i = 1; i < n; i++) {
            int num = nums[i];
            for (int j = 1; j <= target; j++) {
                if (j >= num) {
                    //选择和不选择的结果都可以根据之前的已知的结果得到，来判断当前的结果
                    dp[i][j] = dp[i - 1][j] | dp[i - 1][j - num];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[n - 1][target];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/fen-ge-deng-he-zi-ji-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210416090256355](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416090256.png)

#### （2）动态规划优化

![image-20210416090907516](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416090908.png)

![image-20210416090951077](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210416090951.png)

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n = nums.length;
        if (n < 2) {
            return false;
        }
        int sum = 0, maxNum = 0;
        for (int num : nums) {
            sum += num;
            maxNum = Math.max(maxNum, num);
        }
        if (sum % 2 != 0) {
            return false;
        }
        int target = sum / 2;
        if (maxNum > target) {
            return false;
        }
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        for (int i = 0; i < n; i++) {
            int num = nums[i];
             /*dp[n] (n <= j)其实是dp[i - 1][m], dp[m](m > j)其实是dp[i][m]
                dp[j] = dp[j] || dp[j - nums[i]];   这里的左边的dp[j]是我们需要更新的,当前层第i层的状态,右边的dp[j]是第i-1层的状态
                按照思路其实可以修改为dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i]];
                因为我们dp[i][j]只与上一层以及左上方相关, 如果我们从左到右更新, 那么dp[k] (k < j)其实是dp[i][k],不符合要求
                如果我们从右往左更新,那么左边的状态没有更新过, dp[k] (k < j)是dp[i - 1][k]符合要求
                j就像一个轮胎,它滚过去的部分就是新的状态, 在它之前没有滚过去的部分就是旧状态*/
            for (int j = target; j >= num; --j) {
                dp[j] |= dp[j - num];
            }
        }
        return dp[target];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/fen-ge-deng-he-zi-ji-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417023029108](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417023030.png)

```java
public class Solution {

    public boolean canPartition(int[] nums) {
        int len = nums.length;
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if ((sum & 1) == 1) {
            return false;
        }

        int target = sum / 2;
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
		//等于true是，可以直接选自身，和也是自身，所以dp[nums[0]]总是成立的
        //小于等于就是为了防止数组越界如果nums[0]=8,而target是5，数组长度是target+1=6,越界了
        if (nums[0] <= target) {
            dp[nums[0]] = true;
        }
        for (int i = 1; i < len; i++) {
            for (int j = target; nums[i] <= j; j--) {
                //如果上层的dp[j]是true的，那或的结果肯定就是true的
                if (dp[target]) {
                    return true;
                }
                dp[j] = dp[j] || dp[j - nums[i]];
            }
        }
        return dp[target];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/partition-equal-subset-sum/solution/0-1-bei-bao-wen-ti-xiang-jie-zhen-dui-ben-ti-de-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417024544285](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417024544.png)



# 拓扑排序

## 111、[课程表](https://leetcode-cn.com/problems/course-schedule/)

![image-20210417032440571](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417032441.png)

### 思路

![![img](https://pic.leetcode-cn.com/e2dee0b287ce8f27ec17b97f0ff9b937a5f1117dac526507cf7368f387e84dea-207-2.png)](https://pic.leetcode-cn.com/1c2294cf156a56b90a5d750b6cded3b80e92ab8821abbcb47296cc20845d162a-207-1.png)

![img](https://pic.leetcode-cn.com/e2dee0b287ce8f27ec17b97f0ff9b937a5f1117dac526507cf7368f387e84dea-207-2.png)



![img](https://pic.leetcode-cn.com/56b1fbe4024efb04a0a36b40d8da65f8f726a71e09b274050100a0c7c41bc352-207-3.png)



<img src="https://pic.leetcode-cn.com/0256b4b045ec7e1f835a6b419c54f1c14701d567d99f182194634eb00bf67782-207-4.png" alt="img" />



<img src="https://pic.leetcode-cn.com/0256b4b045ec7e1f835a6b419c54f1c14701d567d99f182194634eb00bf67782-207-4.png" alt="img" style="zoom:67%;" />

![img](https://pic.leetcode-cn.com/deddd19ff3ce401f93e4f01e4fed732f35875f1430b34833b4045961c3181eb2-207-5.png)



![img](https://pic.leetcode-cn.com/37ebca02fc73ff19455f9a3d63fe830c739527b4598df94e8c8fd3f9fc0ad629-207-6.png)



![img](https://pic.leetcode-cn.com/79e50d9a93bc6a53601ee6cb752ad1192bb9d8b2a42919ea4b27efd22e06ff99-207-7.png)



![img](https://pic.leetcode-cn.com/ff4a83824f20a76a91ba55c7c856521d959d8b85391c2aac819ca8adbddbb730-207-8.png)

![img](https://pic.leetcode-cn.com/768d83755ad94c1967b8cfd17feef41bb08085374771203dcb12fbc2c3109af1-207-9.png)

![img](https://pic.leetcode-cn.com/9bfab9cd56b2541c7c310a5ee054c98ef7d4b5188ad8ff606098903a1b2aa5d5-207-10.png)



![img](https://pic.leetcode-cn.com/e91ef7c5d01de19f3ef7126e3503430867f897d01f81b7a7607dd551a8743786-207-11.png)

![img](https://pic.leetcode-cn.com/7fcc5454f1562a1b231aa1fba29bd023c719730257776a10a64c5f5282660fb8-207-12.png)

![img](https://pic.leetcode-cn.com/e580001fca6eeed32f3c44ce0840ed67dfdca47bd88910d7f8fa19f040529d08-207-13.png)

![image-20210417043955641](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417043956.png)



![image-20210417033717652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417033718.png)

![image-20210417033814607](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417033815.png)

#### （1）拓扑排序BFS Kahn算法

![image-20210417040027994](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417040028.png)

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int[] indegrees = new int[numCourses];
        List<List<Integer>> adjacency = new ArrayList<>();
        Queue<Integer> queue = new LinkedList<>();
        for(int i = 0; i < numCourses; i++)
            adjacency.add(new ArrayList<>());
        // Get the indegree and adjacency of every course.
        for(int[] cp : prerequisites) {
            indegrees[cp[0]]++;
            adjacency.get(cp[1]).add(cp[0]);
        }
        // Get all the courses with the indegree of 0.
        for(int i = 0; i < numCourses; i++)
            //这里是添加下标，不是值，下标才是课程的编号
            if(indegrees[i] == 0) queue.add(i);
        // BFS TopSort.
        while(!queue.isEmpty()) {
            int pre = queue.poll();
            numCourses--;
            for(int cur : adjacency.get(pre))
                if(--indegrees[cur] == 0) queue.add(cur);
        }
        return numCourses == 0;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/course-schedule/solution/course-schedule-tuo-bu-pai-xu-bfsdfsliang-chong-fa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210417043526247](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417043526.png)

#### （2）DFS深度优先遍历

![image-20210417050419550](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417050420.png)

![image-20210417050501308](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417050501.png)

![img](https://assets.leetcode-cn.com/solution-static/207/2.png)

![img](https://assets.leetcode-cn.com/solution-static/207/3.png)

![img](https://assets.leetcode-cn.com/solution-static/207/4.png)

![img](https://assets.leetcode-cn.com/solution-static/207/5.png)

![img](https://assets.leetcode-cn.com/solution-static/207/6.png)

![img](https://assets.leetcode-cn.com/solution-static/207/7.png)

![img](https://assets.leetcode-cn.com/solution-static/207/8.png)

![image-20210417050645266](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417050646.png)

![img](https://assets.leetcode-cn.com/solution-static/207/10.png)

![img](https://assets.leetcode-cn.com/solution-static/207/11.png)

![img](https://assets.leetcode-cn.com/solution-static/207/12.png)

![img](https://assets.leetcode-cn.com/solution-static/207/13.png)

![img](https://assets.leetcode-cn.com/solution-static/207/14.png)

![img](https://assets.leetcode-cn.com/solution-static/207/15.png)

![img](https://assets.leetcode-cn.com/solution-static/207/16.png)

![img](https://assets.leetcode-cn.com/solution-static/207/17.png)

![img](https://assets.leetcode-cn.com/solution-static/207/18.png)

![img](https://assets.leetcode-cn.com/solution-static/207/19.png)

![img](https://assets.leetcode-cn.com/solution-static/207/20.png)

![image-20210417050938633](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417050939.png)

![image-20210417052251303](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417052251.png)

```java
class Solution {
    List<List<Integer>> edges;
    int[] visited;
    //是否有环，true表示没有环，初始为true，递归的时候再修改值
    boolean valid = true;

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        edges = new ArrayList<List<Integer>>();
        for (int i = 0; i < numCourses; ++i) {
            edges.add(new ArrayList<Integer>());
        }
        visited = new int[numCourses];
        for (int[] info : prerequisites) {
            edges.get(info[1]).add(info[0]);
        }
        for (int i = 0; i < numCourses && valid; ++i) {
            if (visited[i] == 0) {
                dfs(i);
            }
        }
        return valid;
    }

    public void dfs(int u) {
        visited[u] = 1;
        for (int v: edges.get(u)) {
            if (visited[v] == 0) {
                dfs(v);
                if (!valid) {
                    return;
                }
            } else if (visited[v] == 1) {
                valid = false;
                return;
            }
        }
        visited[u] = 2;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/course-schedule/solution/ke-cheng-biao-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417053754123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417053754.png)

```java
import java.util.HashSet;

public class Solution {

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        if (numCourses <= 0) {
            return false;
        }
        int plen = prerequisites.length;
        if (plen == 0) {
            return true;
        }
        int[] marked = new int[numCourses];

        // 初始化有向图 begin
        HashSet<Integer>[] graph = new HashSet[numCourses];
        for (int i = 0; i < numCourses; i++) {
            graph[i] = new HashSet<>();
        }
        // 初始化有向图 end
        // 有向图的 key 是前驱结点，value 是后继结点的集合，key就是从一个点出发，有多少点跟它相         //连,这些相连的点组成的集合就成了value了
        for (int[] p : prerequisites) {
            graph[p[1]].add(p[0]);
        }

        for (int i = 0; i < numCourses; i++) {
            if (dfs(i, graph, marked)) {
                // 注意方法的语义，如果图中存在环，表示课程任务不能完成，应该返回 false
                return false;
            }
        }
        // 在遍历的过程中，一直 dfs 都没有遇到已经重复访问的结点，就表示有向图中没有环
        // 所有课程任务可以完成，应该返回 true
        return true;
    }

    /**
     * 注意这个 dfs 方法的语义
     * @param i      当前访问的课程结点
     * @param graph
     * @param marked 如果 == 1 表示正在访问中，如果 == 2 表示已经访问完了
     * @return true 表示图中存在环，false 表示访问过了，不用再访问了
     */
    private boolean dfs(int i,
                        HashSet<Integer>[] graph,
                        int[] marked) {
        // 如果访问过了，就不用再访问了
        if (marked[i] == 1) {
            // 从正在访问中，到正在访问中，表示遇到了环
            return true;
        }

        if (marked[i] == 2) {
            // 表示在访问的过程中没有遇到环，这个节点访问过了
            return false;
        }
        // 走到这里，是因为初始化呢，此时 marked[i] == 0
        // 表示正在访问中
        marked[i] = 1;
        // 后继结点的集合
        HashSet<Integer> successorNodes = graph[i];

        for (Integer successor : successorNodes) {
            if (dfs(successor, graph, marked)) {
                // 层层递归返回 true ，表示图中存在环
                return true;
            }
        }
        // i 的所有后继结点都访问完了，都没有存在环，则这个结点就可以被标记为已经访问结束
        // 状态设置为 2
        marked[i] = 2;
        // false 表示图中不存在环
        return false;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/course-schedule/solution/tuo-bu-pai-xu-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417054916176](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417054916.png)

## 112、[课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)

![image-20210417055036253](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417055036.png)

### 思路

#### （1）DFS

```java
import java.util.HashSet;
import java.util.Stack;

/**
 * @author liwei
 * @date 18/6/24 下午4:10
 */
public class Solution3 {

    /**
     * @param numCourses
     * @param prerequisites
     * @return
     */
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses <= 0) {
            // 连课程数目都没有，就根本没有办法完成练习了，根据题意应该返回空数组
            return new int[0];
        }
        int plen = prerequisites.length;
        if (plen == 0) {
            // 没有有向边，则表示不存在课程依赖，任务一定可以完成
            int[] ret = new int[numCourses];
            for (int i = 0; i < numCourses; i++) {
                ret[i] = i;
            }
            return ret;
        }
        int[] marked = new int[numCourses];
        // 初始化有向图 begin
        HashSet<Integer>[] graph = new HashSet[numCourses];
        for (int i = 0; i < numCourses; i++) {
            graph[i] = new HashSet<>();
        }
        // 初始化有向图 end
        // 有向图的 key 是前驱结点，value 是后继结点的集合
        for (int[] p : prerequisites) {
            //这里一定要注意是（1,0）是0-》1，是寻找0的相连的节点，所以key是前驱节点
            graph[p[1]].add(p[0]);
        }
        // 使用 Stack 或者 List 记录递归的顺序，这里使用 Stack
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < numCourses; i++) {
            if (dfs(i, graph, marked, stack)) {
                // 注意方法的语义，如果图中存在环，表示课程任务不能完成，应该返回空数组
                return new int[0];
            }
        }
        // 在遍历的过程中，一直 dfs 都没有遇到已经重复访问的结点，就表示有向图中没有环
        // 所有课程任务可以完成，应该返回 true
        // 下面这个断言一定成立，这是拓扑排序告诉我们的结论
        assert stack.size() == numCourses;
        int[] ret = new int[numCourses];
        // 想想要怎么得到结论，我们的 dfs 是一致将后继结点进行 dfs 的
        // 所以压在栈底的元素，一定是那个没有后继课程的结点
        // 那个没有前驱的课程，一定在栈顶，所以课程学习的顺序就应该是从栈顶到栈底
        // 依次出栈就好了
        for (int i = 0; i < numCourses; i++) {
            ret[i] = stack.pop();
        }
        return ret;
    }

    /**
     * 注意这个 dfs 方法的语义
     *
     * @param i      当前访问的课程结点
     * @param graph
     * @param marked 如果 == 1 表示正在访问中，如果 == 2 表示已经访问完了
     * @return true 表示图中存在环，false 表示访问过了，不用再访问了
     */
    private boolean dfs(int i,
                        HashSet<Integer>[] graph,
                        int[] marked,
                        Stack<Integer> stack) {
        // 如果访问过了，就不用再访问了
        if (marked[i] == 1) {
            // 从正在访问中，到正在访问中，表示遇到了环
            return true;
        }
        if (marked[i] == 2) {
            // 表示在访问的过程中没有遇到环，这个节点访问过了
            return false;
        }
        // 走到这里，是因为初始化呢，此时 marked[i] == 0
        // 表示正在访问中
        marked[i] = 1;
        // 后继结点的集合
        HashSet<Integer> successorNodes = graph[i];
        for (Integer successor : successorNodes) {
            if (dfs(successor, graph, marked, stack)) {
                // 层层递归返回 true ，表示图中存在环
                return true;
            }
        }
        // i 的所有后继结点都访问完了，都没有存在环，则这个结点就可以被标记为已经访问结束
        // 状态设置为 2
        marked[i] = 2;
        stack.add(i);
        // false 表示图中不存在环
        return false;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/course-schedule-ii/solution/tuo-bu-pai-xu-shen-du-you-xian-bian-li-python-dai-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210417230104841](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417230105.png)



#### （2）BFS

![image-20210417183511986](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417230033.png)

```java
    // 方法 1 最简单的 BFS
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 0) return new int[0];
        int[] inDegrees = new int[numCourses];
        // 建立入度表
        for (int[] p : prerequisites) { // 对于有先修课的课程，计算有几门先修课
            inDegrees[p[0]]++;
        }
        // 入度为0的节点队列
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < inDegrees.length; i++) {
            if (inDegrees[i] == 0) queue.offer(i);
        }
        int count = 0;  // 记录可以学完的课程数量
        int[] res = new int[numCourses];  // 可以学完的课程
        // 根据提供的先修课列表，删除入度为 0 的节点
        while (!queue.isEmpty()){
            int curr = queue.poll();
            res[count++] = curr;   // 将可以学完的课程加入结果当中
            for (int[] p : prerequisites) {
                if (p[1] == curr){
                    inDegrees[p[0]]--;
                    if (inDegrees[p[0]] == 0) queue.offer(p[0]);
                }
            }
        }
        if (count == numCourses) return res;
        return new int[0];
    }

作者：kelly2018
链接：https://leetcode-cn.com/problems/course-schedule-ii/solution/java-jian-dan-hao-li-jie-de-tuo-bu-pai-xu-by-kelly/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417183537036](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417183537.png)

###### 复杂度分析

![image-20210417225956432](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417225956.png)



## 113、[括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

![image-20210417230711032](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417230711.png)

### 思路

#### （1）回溯减法DFS

![image-20210417233350103](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417233351.png)

![image-20210417233449278](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417233449.png)

```java
class Solution {
    List<String> res = new ArrayList<>();
    public List<String> generateParenthesis(int n) {
        dfs(n, n, "");
        return res;
    }

    private void dfs(int left, int right, String curStr) {
        if (left == 0 && right == 0) { // 左右括号都不剩余了，递归终止，否则说明还有可以配对			//的括号，继续递归去拼接
            res.add(curStr);
            return;
        }

        if (left > 0) { // 如果左括号还剩余的话，可以拼接左括号
            dfs(left - 1, right, curStr + "(");
        }
        if (right > left) { // 如果右括号剩余多于左括号剩余的话，可以拼接右括号
            dfs(left, right - 1, curStr + ")");
        }
    }

作者：sweetiee
链接：https://leetcode-cn.com/problems/generate-parentheses/solution/jian-dan-dfsmiao-dong-by-sweetiee/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210417233928339](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417233928.png)

#### （2）回溯加法DFS

![image-20210417234713503](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210417234714.png)

如果我们不用减法，使用加法，即 `left` 表示「左括号还有几个没有用掉」，`right` 表示「右括号还有几个没有用掉」，可以画出另一棵递归树。

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {

    // 做加法

    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        // 特判
        if (n == 0) {
            return res;
        }

        dfs("", 0, 0, n, res);
        return res;
    }

    /**
     * @param curStr 当前递归得到的结果
     * @param left   左括号已经用了几个
     * @param right  右括号已经用了几个
     * @param n      左括号、右括号一共得用几个
     * @param res    结果集
     */
    private void dfs(String curStr, int left, int right, int n, List<String> res) {
        if (left == n && right == n) {
            res.add(curStr);
            return;
        }

        // 剪枝
        if (left < right) {
            return;
        }

        if (left < n) {
            dfs(curStr + "(", left + 1, right, n, res);
        }
        if (right < n) {
            dfs(curStr + ")", left, right + 1, n, res);
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/generate-parentheses/solution/hui-su-suan-fa-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）BFS队列

![image-20210418000254122](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418000254.png)

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class Solution {

    class Node {
        /**
         * 当前得到的字符串
         */
        private String res;
        /**
         * 剩余左括号数量
         */
        private int left;
        /**
         * 剩余右括号数量
         */
        private int right;

        public Node(String str, int left, int right) {
            this.res = str;
            this.left = left;
            this.right = right;
        }
    }

    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if (n == 0) {
            return res;
        }
        Queue<Node> queue = new LinkedList<>();
        queue.offer(new Node("", n, n));

        while (!queue.isEmpty()) {

            Node curNode = queue.poll();
            if (curNode.left == 0 && curNode.right == 0) {
                res.add(curNode.res);
            }
            if (curNode.left > 0) {
                queue.offer(new Node(curNode.res + "(", curNode.left - 1, curNode.right));
            }
            if (curNode.right > 0 && curNode.left < curNode.right) {
                queue.offer(new Node(curNode.res + ")", curNode.left, curNode.right - 1));
            }
        }
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/generate-parentheses/solution/hui-su-suan-fa-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210418000304107](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418000304.png)



## 114、[单词拆分](https://leetcode-cn.com/problems/word-break/)

![image-20210418025041972](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418025043.png)

### 思路

#### （1）完全背包动态规划

![image-20210418030921063](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418030921.png)

![image-20210418031111557](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418031112.png)

![image-20210418033631620](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418033632.png)

![image-20210418034137373](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418034137.png)

```java
class Solution {
    /*
        动态规划算法，dp[i]表示s前i个字符能否拆分
        转移方程：dp[j] = dp[i] && check(s[i+1, j]);
        check(s[i+1, j])就是判断i+1到j这一段字符是否能够拆分
        其实，调整遍历顺序，这等价于s[i+1, j]是否是wordDict中的元素
        这个举个例子就很容易理解。
        假如wordDict=["apple", "pen", "code"],s = "applepencode";
        dp[8] = dp[5] + check("pen")
        翻译一下：前八位能否拆分取决于前五位能否拆分，加上五到八位是否属于字典
        （注意：i的顺序是从j-1 -> 0哦~
    */

    public HashMap<String, Boolean> hash = new HashMap<>();
    public boolean wordBreak(String s, List<String> wordDict) {
        boolean[] dp = new boolean[s.length()+1];
        
        //方便check，构建一个哈希表
        for(String word : wordDict){
            hash.put(word, true);
        }

        //初始化
        dp[0] = true;

        //遍历
        for(int j = 1; j <= s.length(); j++){
            for(int i = j-1; i >= 0; i--){
                dp[j] = dp[i] && check(s.substring(i, j));
                if(dp[j])   break;
            }
        }

        return dp[s.length()];
    }

    public boolean check(String s){
        return hash.getOrDefault(s, false);
    }
}

作者：RED_DEVIL
链接：https://leetcode-cn.com/problems/word-break/solution/dan-ci-chai-fen-ju-jue-zhuang-xcong-jian-dan-de-xi/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## =============字符串==============



## 115、[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

![image-20210418161220836](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418161221.png)

### 思路

#### （1）动态规划

![image-20210418162452403](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418162452.png)

```java
public class Solution {

    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }

        int maxLen = 1;
        int begin = 0;
        // dp[i][j] 表示 s[i..j] 是否是回文串
        boolean[][] dp = new boolean[len][len];
        // 初始化：所有长度为 1 的子串都是回文串
        for (int i = 0; i < len; i++) {
            dp[i][i] = true;
        }

        char[] charArray = s.toCharArray();
        // 递推开始
        // 先枚举子串长度
        for (int L = 2; L <= len; L++) {
            // 枚举左边界，左边界的上限设置可以宽松一些
            for (int i = 0; i < len; i++) {
                // 由 L 和 i 可以确定右边界，即 j - i + 1 = L 得
                int j = L + i - 1;
                // 如果右边界越界，就可以退出当前循环
                if (j >= len) {
                    break;
                }

                if (charArray[i] != charArray[j]) {
                    dp[i][j] = false;
                } else {
                    if (j - i < 3) {
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i + 1][j - 1];
                    }
                }

                // 只要 dp[i][L] == true 成立，就表示子串 s[i..L] 是回文，此时记录回文长度和起始位置
                if (dp[i][j] && j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    begin = i;
                }
            }
        }
        return s.substring(begin, begin + maxLen);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zui-chang-hui-wen-zi-chuan-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210418163707624](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418163708.png)



![image-20210418163653643](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210418163654.png)



#### （2）中心扩展

![image-20210419021358757](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419021359.png)

![image-20210419022825336](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419022825.png)

```java
public String longestPalindrome1(String s) {

        if (s == null || s.length() == 0) {
            return "";
        }
        int strLen = s.length();
        int left = 0;
        int right = 0;
        int len = 1;
        int maxStart = 0;
        int maxLen = 0;

        for (int i = 0; i < strLen; i++) {
            left = i - 1;
            right = i + 1;
            while (left >= 0 && s.charAt(left) == s.charAt(i)) {
                len++;
                left--;
            }
            while (right < strLen && s.charAt(right) == s.charAt(i)) {
                len++;
                right++;
            }
            while (left >= 0 && right < strLen && s.charAt(right) == s.charAt(left)) {
                len = len + 2;
                left--;
                right++;
            }
            if (len > maxLen) {
                maxLen = len;
                maxStart = left;
            }
            len = 1;
        }
        return s.substring(maxStart + 1, maxStart + maxLen + 1);

    }

作者：reedfan
链接：https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-fa-he-dong-tai-gui-hua-by-reedfa/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210419022513489](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419022514.png)

#### （3）中心扩展2

![image-20210419024323973](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419024324.png)

```java
public String longestPalindrome(String s) {
    if (s == null || s.length() < 1) return "";
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            //len-1主要是针对中心是两个字符的情况，两个中心离左边更远点
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
}

作者：windliang
链接：https://leetcode-cn.com/problems/longest-palindromic-substring/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-bao-gu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210419030219690](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419030220.png)

###### 复杂度分析

![image-20210419031356421](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419031356.png)

## 116、[ Z 字形变换](https://leetcode-cn.com/problems/zigzag-conversion/)

![image-20210419035929398](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419035929.png)



### 思路

![image-20210419045234733](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419045235.png)

#### （1）上下移动遍历

![image-20210419035912749](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419035913.png)

![image-20210419043946084](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419043947.png)

![img](https://pic.leetcode-cn.com/c7f53f8480c33925ecae3cd91ac4b20337949de67a255663cc550bdc68ba9315-Picture1.png)

![img](https://pic.leetcode-cn.com/bfcbaa31dc07dbf0e68a854e6da8445abe67432d3b624ae627f1195dd3c54d6e-Picture2.png)

![img](https://pic.leetcode-cn.com/4604c49a47c1cf995f292f17313104fc5720a340a3bd649410734ecace7108a7-Picture3.png)

![img](https://pic.leetcode-cn.com/4ecbe654add7b2b80d4dd81038e4681607b7cbef469fa27ae954fa789d13ed82-Picture4.png)

![img](https://pic.leetcode-cn.com/d26d1faedbe13f78a94c28047fc4dc91fb72419452b3edae669e44a4d730d5ff-Picture5.png)

![img](https://pic.leetcode-cn.com/e8fdc68fb3029017333e01f9a3e25e03675f87260e49f53fee7938b4d02ca997-Picture6.png)

![img](https://pic.leetcode-cn.com/e1db50ce219e68d2d6a57b197b932088731dd09afc788ee818e0b38880458bb5-Picture7.png)

![img](https://pic.leetcode-cn.com/ebbed8592bd11014e81affb8af6df3e713d88ae0e8003f4f989459d7694e475c-Picture8.png)

![img](https://pic.leetcode-cn.com/5c7b6ebd51be1e16eab6c1ccd3121d6dae2aff3b61fa07ecb21235250c33e76c-Picture9.png)

![image-20210419044250660](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419044251.png)

```java
class Solution {
    public String convert(String s, int numRows) {
        if(numRows < 2) return s;
        List<StringBuilder> rows = new ArrayList<StringBuilder>();
        for(int i = 0; i < numRows; i++) rows.add(new StringBuilder());
        int i = 0, flag = -1;
        for(char c : s.toCharArray()) {
            rows.get(i).append(c);
            if(i == 0 || i == numRows -1) flag = - flag;
            i += flag;
        }
        StringBuilder res = new StringBuilder();
        for(StringBuilder row : rows) res.append(row);
        return res.toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zigzag-conversion/solution/zzi-xing-bian-huan-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210419045130169](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419045130.png)

![image-20210419045155453](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419045155.png)

#### （2）观察规律

![image-20210419045544761](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419045545.png)

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;
        StringBuilder[] rows = new StringBuilder[numRows];
        for(int i = 0; i < numRows; i++) rows[i] = new StringBuilder();
        int n = 2 * numRows - 2;
        for (int i = 0; i < s.length(); i++) {
            int x = i % n;
            rows[Math.min(x, n - x)].append(s.charAt(i));
        }
        return String.join("", rows);
    }
}

作者：zoffer
链接：https://leetcode-cn.com/problems/zigzag-conversion/solution/ji-jian-jie-fa-by-ijzqardmbd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 117、[字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

![image-20210419051220273](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419051220.png)

![image-20210419051232738](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419051233.png)

![image-20210419051243319](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419051243.png)

### 思路

#### （1）尽量不使用库函数、一次遍历

![image-20210419055125212](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419055125.png)

![image-20210419055058227](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419055058.png)

![image-20210419053130140](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419053130.png)



![image-20210419052741779](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419052742.png)

```java
public class Solution {

    public int myAtoi(String str) {
        int len = str.length();
        // str.charAt(i) 方法回去检查下标的合法性，一般先转换成字符数组
        char[] charArray = str.toCharArray();

        // 1、去除前导空格
        int index = 0;
        while (index < len && charArray[index] == ' ') {
            index++;
        }

        // 2、如果已经遍历完成（针对极端用例 "      "）
        if (index == len) {
            return 0;
        }

        // 3、如果出现符号字符，仅第 1 个有效，并记录正负
        int sign = 1;
        char firstChar = charArray[index];
        if (firstChar == '+') {
            index++;
        } else if (firstChar == '-') {
            index++;
            sign = -1;
        }

        // 4、将后续出现的数字字符进行转换
        // 不能使用 long 类型，这是题目说的
        int res = 0;
        while (index < len) {
            char currChar = charArray[index];
            // 4.1 先判断不合法的情况
            if (currChar > '9' || currChar < '0') {
                break;
            }

            // 题目中说：环境只能存储 32 位大小的有符号整数，因此，需要提前判：断乘以 10 以后是否越界
            if (res > Integer.MAX_VALUE / 10 || (res == Integer.MAX_VALUE / 10 && (currChar - '0') > Integer.MAX_VALUE % 10)) {
                return Integer.MAX_VALUE;
            }
            if (res < Integer.MIN_VALUE / 10 || (res == Integer.MIN_VALUE / 10 && (currChar - '0') > -(Integer.MIN_VALUE % 10))) {
                return Integer.MIN_VALUE;
            }

            // 4.2 合法的情况下，才考虑转换，每一步都把符号位乘进去
            res = res * 10 + sign * (currChar - '0');
            index++;
        }
        return res;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        String str = "2147483646";
        int res = solution.myAtoi(str);
        System.out.println(res);

        System.out.println(Integer.MAX_VALUE);
        System.out.println(Integer.MIN_VALUE);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/string-to-integer-atoi/solution/jin-liang-bu-shi-yong-ku-han-shu-nai-xin-diao-shi-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210419052816618](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419052817.png)

![image-20210419054937112](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210419054937.png)

#### （2）自动机

复杂，待研究

#### （3）正则表达式

效率差

## 118、[整数转罗马数字](https://leetcode-cn.com/problems/integer-to-roman/)

![image-20210420022010087](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420022011.png)

![image-20210420022027036](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420022027.png)

### 思路

#### （1）贪心算法

![image-20210420024305288](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420024305.png)

![image-20210420024345209](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420024345.png)

![image-20210420024503917](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420024504.png)

![image-20210420024741692](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420024742.png)

```java
public class Solution {

    public String intToRoman(int num) {
        // 把阿拉伯数字与罗马数字可能出现的所有情况和对应关系，放在两个数组中，并且按照阿拉伯数字的大小降序排列
        int[] nums = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] romans = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        StringBuilder stringBuilder = new StringBuilder();
        int index = 0;
        while (index < 13) {
            // 特别注意：这里是等号
            while (num >= nums[index]) {
                stringBuilder.append(romans[index]);
                num -= nums[index];
            }
            index++;
        }
        return stringBuilder.toString();
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/integer-to-roman/solution/tan-xin-suan-fa-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210420024859769](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420024900.png)

![image-20210420025121198](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420025121.png)

## 119、[罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

![image-20210420030121190](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420030121.png)

![image-20210420030258041](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420030258.png)

### 思路

#### （1）列表分析规律

![image-20210420031609867](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420031610.png)

![image-20210420031756932](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420031757.png)



## 120、[最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

![image-20210420031907975](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420031908.png)

### 思路

#### （1）横向扫描

![image-20210420035118911](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420035119.png)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        String prefix = strs[0];
        int count = strs.length;
        for (int i = 1; i < count; i++) {
            prefix = longestCommonPrefix(prefix, strs[i]);
            if (prefix.length() == 0) {
                break;
            }
        }
        return prefix;
    }

    public String longestCommonPrefix(String str1, String str2) {
        int length = Math.min(str1.length(), str2.length());
        int index = 0;
        while (index < length && str1.charAt(index) == str2.charAt(index)) {
            index++;
        }
        return str1.substring(0, index);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/longest-common-prefix/solution/zui-chang-gong-gong-qian-zhui-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### **复杂度分析**

![image-20210420035411490](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420035411.png)

#### （2）纵向扫描

![image-20210420040326249](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420040326.png)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        int length = strs[0].length();
        int count = strs.length;
        for (int i = 0; i < length; i++) {
            char c = strs[0].charAt(i);
            for (int j = 1; j < count; j++) {
                if (i == strs[j].length() || strs[j].charAt(i) != c) {
                    return strs[0].substring(0, i);
                }
            }
        }
        return strs[0];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/longest-common-prefix/solution/zui-chang-gong-gong-qian-zhui-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210420040406593](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420040407.png)

## 121、[外观数列](https://leetcode-cn.com/problems/count-and-say/)

![image-20210420044038236](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420044038.png)

![image-20210420044051219](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420044051.png)

### 思路

#### （1）双指针 递归

![image-20210420045223124](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420045223.png)

```java
class Solution {
    public String countAndSay(int n) {
        // 递归终止条件
        if (n == 1) {
            return "1";
        }
        // 获取到上一层的字符串
        String s = countAndSay(n - 1);
        StringBuilder result = new StringBuilder();
        // 记录每个数字的开始索引
        int start = 0;
        for (int i = 1; i < s.length(); i++) {
            // 当数字发生改变时执行
            if (s.charAt(i) != s.charAt(start)) {
                result.append(i - start).append(s.charAt(start));
                start = i;
            }
        }
        // 字符串最后一个数字
        //因为在 for 循环中，只有当数字发生变化时，才统计之前的数字，进行字符串的拼接。对于最后           //的数字，并没有在 for 循环中进行处理，所以需要单独处理。
        //(s.length() - start) 计算的是字符串后缀中，和最后一个数字相等且连续的字符个数，不一定          //为 1 个。比如：1211，(s.length() - start) 的结果就为 2。
        result.append(s.length() - start).append(s.charAt(start));
        return result.toString();
    }
}

作者：yixingzhang
链接：https://leetcode-cn.com/problems/count-and-say/solution/xun-huan-he-di-gui-liang-chong-jie-fa-di-oof8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```





![图示](https://pic.leetcode-cn.com/1602408534-djHCrZ-38_%E5%A4%96%E8%A7%82%E6%95%B0%E5%88%97.gif)

###### 复杂度分析

![image-20210420050055214](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420050055.png)

#### （2）循环

![image-20210420051449653](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420051450.png)

```java
class Solution {
    public String countAndSay(int n) {
        StringBuilder result = new StringBuilder();
        result.append(1);
        //这里从1开始，是因为第一层已经加入了结果了，就是“1”
        for (int i = 1; i < n; i++) {
            // 记录当前行的字符串
            StringBuilder s = new StringBuilder();
            // 记录每个数字的开始索引
            int start = 0;
            for (int j = 1; j < result.length(); j++) {
                // 当数字发生改变时执行
                if (result.charAt(j) != result.charAt(start)) {
                    s.append(j - start).append(result.charAt(start));
                    start = j;
                }
            }
            // 字符串最后一个数字，使用上一层的结果来计算，不是当前层的s的长度来计算
            s.append(result.length() - start).append(result.charAt(start));
            result = s;
        }
        return result.toString();
    }
}

作者：yixingzhang
链接：https://leetcode-cn.com/problems/count-and-say/solution/xun-huan-he-di-gui-liang-chong-jie-fa-di-oof8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210420051531202](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210420051531.png)

## 122、[字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

![image-20210421013919742](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421013921.png)

### 思路

#### （1）普通竖式

![image-20210421023624308](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421023625.png)

![image-20210421025624973](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421025625.png)

```java
class Solution {
    /**
    * 计算形式
    *    num1
    *  x num2
    *  ------
    *  result
    */
    public String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }
        // 保存计算结果
        String res = "0";
        
        // num2 逐位与 num1 相乘
        for (int i = num2.length() - 1; i >= 0; i--) {
            int carry = 0;
            // 保存 num2 第i位数字与 num1 相乘的结果
            StringBuilder temp = new StringBuilder();
            // 补 0 
            for (int j = 0; j < num2.length() - 1 - i; j++) {
                temp.append(0);
            }
            int n2 = num2.charAt(i) - '0';
            
            // num2 的第 i 位数字 n2 与 num1 相乘
            for (int j = num1.length() - 1; j >= 0 || carry != 0; j--) {
                int n1 = j < 0 ? 0 : num1.charAt(j) - '0';
                int product = (n1 * n2 + carry) % 10;
                temp.append(product);
                carry = (n1 * n2 + carry) / 10;
            }
            // 将当前结果与新计算的结果求和作为新的结果
            res = addStrings(res, temp.reverse().toString());
        }
        return res;
    }

    /**
     * 对两个字符串数字进行相加，返回字符串形式的和
     */
    public String addStrings(String num1, String num2) {
        StringBuilder builder = new StringBuilder();
        int carry = 0;
        for (int i = num1.length() - 1, j = num2.length() - 1;
             i >= 0 || j >= 0 || carry != 0;
             i--, j--) {
            int x = i < 0 ? 0 : num1.charAt(i) - '0';
            int y = j < 0 ? 0 : num2.charAt(j) - '0';
            int sum = (x + y + carry) % 10;
            builder.append(sum);
            carry = (x + y + carry) / 10;
        }
        return builder.reverse().toString();
    }
}

作者：breezean
链接：https://leetcode-cn.com/problems/multiply-strings/solution/you-hua-ban-shu-shi-da-bai-994-by-breezean/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210421025644187](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421025644.png)

![image-20210421025654449](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421025655.png)



#### （2）优化竖式

![image-20210421025935053](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421025935.png)

![image-20210421031218284](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421031219.png)

![image-20210421031605163](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421031606.png)

![image-20210421033317645](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421033318.png)

![image-20210421033333510](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421033334.png)

![img](https://pic.leetcode-cn.com/1600675705-fZYXsV-file_1600675705556)

```java
class Solution {
    public String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }
        int[] res = new int[num1.length() + num2.length()];
        for (int i = num1.length() - 1; i >= 0; i--) {
            int n1 = num1.charAt(i) - '0';
            for (int j = num2.length() - 1; j >= 0; j--) {
                int n2 = num2.charAt(j) - '0';
                int sum = (res[i + j + 1] + n1 * n2);
                res[i + j + 1] = sum % 10;
                //将所有数据不进位直接存入数组，最后统一进位即可。
                //高位不用考虑进位，可用保存2位数
                //下一轮计算会处理该位置的进位
                // 末一轮也不影响，因为是最高位，直接转字符串结果一样
                //这个可能是0，导致结果的第一位可能是0
                res[i + j] += sum / 10;
            }
        }

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < res.length; i++) {
            //m位乘以n位是m+n-1位到m+n位。
            //因为给的字符串首位不为0啊，所以当索引到i=0,j=0的时候，res[i+j+1]一定就是相乘之后			  //对10取余的部分。而res[i+j]=res[0]可能并没有进位，此时res[0]可能为0.所以特判一			  //下。
            if (i == 0 && res[i] == 0) continue;
            result.append(res[i]);
        }
        return result.toString();
    }
}

作者：breezean
链接：https://leetcode-cn.com/problems/multiply-strings/solution/you-hua-ban-shu-shi-da-bai-994-by-breezean/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
string multiply(string num1, string num2) {
    int m = num1.size(), n = num2.size();
    // 结果最多为 m + n 位数
    vector<int> res(m + n, 0);
    // 从个位数开始逐位相乘
    for (int i = m - 1; i >= 0; i--)
        for (int j = n - 1; j >= 0; j--) {
            int mul = (num1[i]-'0') * (num2[j]-'0');
            // 乘积在 res 对应的索引位置
            int p1 = i + j, p2 = i + j + 1;
            // 叠加到 res 上
            int sum = mul + res[p2];
            res[p2] = sum % 10;
            res[p1] += sum / 10;
        }
    // 结果前缀可能存的 0（未使用的位）
    int i = 0;
    while (i < res.size() && res[i] == 0)
        i++;
    // 将计算结果转化成字符串
    string str;
    for (; i < res.size(); i++)
        str.push_back('0' + res[i]);
    
    return str.size() == 0 ? "0" : str;
}

作者：labuladong
链接：https://leetcode-cn.com/problems/multiply-strings/solution/gao-pin-mian-shi-xi-lie-zi-fu-chuan-cheng-fa-by-la/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210421035116516](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421035117.png)

![image-20210421035533337](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421035533.png)



## 123、[最后一个单词的长度](https://leetcode-cn.com/problems/length-of-last-word/)

### ![image-20210421042335801](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421042336.png)思路

#### （1）跳过空格

![image-20210421042600931](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421042601.png)

```java
class Solution {
    public int lengthOfLastWord(String s) {
        if(s == null || s.length() == 0) return 0;
        int count = 0;
        for(int i = s.length()-1; i >= 0; i--){
            if(s.charAt(i) == ' '){
                if(count == 0) continue;
                break;
            }
            count++;
        }
        return count;        
    }
}
```



###### 复杂度分析

![image-20210421042659024](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421042659.png)

- 时间复杂度：O(n)，`n`为结尾空格和结尾单词总体长度



## 124、[二进制求和](https://leetcode-cn.com/problems/add-binary/)

![image-20210421043837923](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421043838.png)

### 思路

#### （1）简单位相加

![image-20210421044900089](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421044900.png)

```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder ans = new StringBuilder();
        int ca = 0;
        for(int i = a.length() - 1, j = b.length() - 1;i >= 0 || j >= 0; i--, j--) {
            int sum = ca;
            sum += i >= 0 ? a.charAt(i) - '0' : 0;
            sum += j >= 0 ? b.charAt(j) - '0' : 0;
            ans.append(sum % 2);
            ca = sum / 2;
        }
        ans.append(ca == 1 ? ca : "");
        return ans.reverse().toString();
    }
}

作者：guanpengchn
链接：https://leetcode-cn.com/problems/add-binary/solution/hua-jie-suan-fa-67-er-jin-zhi-qiu-he-by-guanpengch/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 我自己写的

```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder res=new StringBuilder();
        int carry=0;
        for(int i=a.length()-1,j=b.length()-1;i >= 0 || j >= 0 || (carry != 0);i--,j--){
            int n1=i<0?0:a.charAt(i)-'0';
            int n2=j<0?0:b.charAt(j)-'0';
            int sum=n1+n2+carry;
            res.append(sum%2);
            carry=sum/2;
        }
        return res.reverse().toString();

    }
}
```

###### 复杂度分析

![image-20210421045041282](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421045041.png)

#### （2）位运算

![image-20210421050328743](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421050329.png)

异或是不带进位的和，相与后再左移一位是得到的进位值 接下来，依然是两个结果相加，重复以上过程就可以了



## 125、[简化路径](https://leetcode-cn.com/problems/simplify-path/)

![image-20210421051258450](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421051259.png)

### 思路

#### （1）栈

![image-20210421051956123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421051956.png)

![image-20210421052134211](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421052134.png)

![image-20210421052652927](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421052653.png)

```java
class Solution {
    public String simplifyPath(String path) {
        Deque<String> stack = new LinkedList<>();
        for (String item : path.split("/")) {
            if (item.equals("..")) {
                if (!stack.isEmpty()) stack.pop();
            } else if (!item.isEmpty() && !item.equals(".")) stack.push(item);
        }
        String res = "";
        //从栈顶遍历到栈底
        for (String d : stack) res = "/" + d + res;
        return res.isEmpty() ? "/" : res;  
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/simplify-path/solution/zhan-by-powcai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210421054115594](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421054116.png)

```java
class Solution {
    public String simplifyPath(String path) {
        Stack<String> stack = new Stack<>();
        // 首先将字符串以 “/” 分隔存储到新的字符数组 str 中
        String[] str = path.split("/");
        for (String s : str) {
            // 如果访问到的是 “..” 则说明要返回上一级,要将当前元素出栈
            if (s.equals("..") ) {
                // 还需判断栈是否为空,否则会报错
                if (!stack.isEmpty() ) {
                    stack.pop();
                }                
            // 如果数组非空并且当前元素不是 “.” 说明当前元素是路径信息，要入栈
            } else if (!s.equals("") && !s.equals(".")) {
                stack.push(s);
            }
        }
        // 如果栈内没有元素说明没有路径信息，返回 “/” 即可
        if (stack.isEmpty()) {
            return "/";
        }
        // 这里用到 StringBuilder 操作字符串，效率高
        StringBuilder ans = new StringBuilder();
        for (int i = 0; i < stack.size(); i++) {
            // 这里从栈底开始拿元素
            ans.append( "/" + stack.get(i) );
        }
        return ans.toString();
    }
}

作者：Jason_H
链接：https://leetcode-cn.com/problems/simplify-path/solution/2020040371medianzhan-zi-fu-shu-zu-ke-bian-zi-fu-ch/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210421054238682](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210421054239.png)



## 126、[复原 IP 地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

![image-20210422002651932](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422002652.png)

### 思路

#### （1）回溯

![image-20210422004220504](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422004221.png)

![image-20210422004256914](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422004257.png)

![image-20210422004306574](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422004307.png)

![image-20210422010330672](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422010331.png)

![image-20210422010126132](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422010126.png)

```java
class Solution {
    public List<String> restoreIpAddresses(String s) {
        List<String> ans = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return ans;
        }
        backtrack(s, ans, 0, new ArrayList<>());
        return ans;
    }
    // pos-当前遍历到 s 字符串中的位置，tmp-当前存放已经确定好的 ip 段的数量
    private void backtrack(String s, List<String> ans, int pos, List<String> tmp) {
        if (tmp.size() == 4) {
            // 如果此时 pos 也刚好遍历完整个 s
            if (pos == s.length()) {
                // join 用法：例如 [[255],[255],[111],[35]] -> 255.255.111.35
                ans.add(String.join(".", tmp));
            }
            // 否则直接返回
            return;
        }
        
        // ip 地址每段最多有三个数字
        for (int i = 1; i <= 3; i++) {
            //等于的时候已经是最后一个字符串了
            // 如果当前位置距离 s 末尾小于 3 就不用再分段了，直接跳出循环即可。
            if (pos + i > s.length()) {
                break;
            }
            
            // 将 s 的子串开始分段
            String segment = s.substring(pos, pos + i);
            int val = Integer.valueOf(segment);
            // 剪枝条件：段的起始位置不能为 0，段拆箱成 int 类型的长度不能大于 255
            if (segment.startsWith("0") && segment.length() > 1 || (i == 3 && val > 255)) {
                continue;
            }
            
            // 符合要求就加入到 tmp 中
            tmp.add(segment);
            // 继续递归遍历下一个位置
            backtrack(s, ans, pos + i, tmp);
            // 回退到上一个元素，即回溯
            tmp.remove(tmp.size() - 1);
        }
    }
}

作者：Jason_H
链接：https://leetcode-cn.com/problems/restore-ip-addresses/solution/2020041693medianhui-su-di-gui-fu-yuan-ip-di-zhi-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210422011518294](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422011518.png)

![image-20210422011626523](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422011627.png)

（2）回溯写法2

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    public List<String> restoreIpAddresses(String s) {
        int len = s.length();
        List<String> res = new ArrayList<>();
        if (len > 12 || len < 4) {
            return res;
        }

        Deque<String> path = new ArrayDeque<>(4);
        dfs(s, len, 0, 4, path, res);
        return res;
    }

    // 需要一个变量记录剩余多少段还没被分割

    private void dfs(String s, int len, int begin, int residue, Deque<String> path, List<String> res) {
        if (begin == len) {
            if (residue == 0) {
                res.add(String.join(".", path));
            }
            return;
        }

        for (int i = begin; i < begin + 3; i++) {
            if (i >= len) {
                break;
            }

            if (residue * 3 < len - i) {
                continue;
            }

            if (judgeIpSegment(s, begin, i)) {
                String currentIpSegment = s.substring(begin, i + 1);
                path.addLast(currentIpSegment);

                dfs(s, len, i + 1, residue - 1, path, res);
                path.removeLast();
            }
        }
    }

    private boolean judgeIpSegment(String s, int left, int right) {
        int len = right - left + 1;
        if (len > 1 && s.charAt(left) == '0') {
            return false;
        }

        int res = 0;
        while (left <= right) {
            res = res * 10 + s.charAt(left) - '0';
            left++;
        }

        return res >= 0 && res <= 255;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/restore-ip-addresses/solution/hui-su-suan-fa-hua-tu-fen-xi-jian-zhi-tiao-jian-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 127、[比较版本号](https://leetcode-cn.com/problems/compare-version-numbers/)

![image-20210422025507049](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422025510.png)

### 思路

#### （1）字符串比较

上边的解法可以成功 `AC`，但是如果数字过大的话，`int` 是无法保存的。所以我们可以不把字符串转为数字，而是直接用字符串比较。

```java
public int compareVersion(String version1, String version2) {
    String[] nums1 = version1.split("\\.");
    String[] nums2 = version2.split("\\.");
    int i = 0, j = 0;
    while (i < nums1.length || j < nums2.length) {
        String num1 = i < nums1.length ? nums1[i] : "0";
        String num2 = j < nums2.length ? nums2[j] : "0";
        int res = compare(num1, num2);
        if (res == 0) {
            i++;
            j++;
        } else {
            return res;
        }
    }
    return 0;
}

private int compare(String num1, String num2) {
    //将高位的 0 去掉
    num1 = removeFrontZero(num1);
    num2 = removeFrontZero(num2);
    //先根据长度进行判断，题目中有说比较整数值，所以可以先根据位数判断
    if (num1.length() > num2.length()) {
        return 1;
    } else if (num1.length() < num2.length()) {
        return -1;
    } else {
        //长度相等的时候
        for (int i = 0; i < num1.length(); i++) {
            if (num1.charAt(i) - num2.charAt(i) > 0) {
                return 1;
            } else if (num1.charAt(i) - num2.charAt(i) < 0) {
                return -1;
            }
        }
        return 0;
    }
}

private String removeFrontZero(String num) {
    int start = 0;
    for (int i = 0; i < num.length(); i++) {
        if (num.charAt(i) == '0') {
            start++;
        } else {
            break;
        }
    }
    return num.substring(start);
}

作者：windliang
链接：https://leetcode-cn.com/problems/compare-version-numbers/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-40/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 128、[去除重复字母](https://leetcode-cn.com/problems/remove-duplicate-letters/)

![image-20210422030937364](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422030938.png)

### 思路

#### （1）单调栈

![image-20210422032706392](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422032707.png)

![image-20210422032756236](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422032756.png)

![image-20210422032834889](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422032835.png)

![image-20210422032944282](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422032944.png)

![image-20210422033139515](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422033140.png)

```java
String removeDuplicateLetters(String s) {
    Stack<Character> stk = new Stack<>();

    // 维护一个计数器记录字符串中字符的数量
    // 因为输入为 ASCII 字符，大小 256 够用了
    int[] count = new int[256];
    for (int i = 0; i < s.length(); i++) {
        count[s.charAt(i)]++;
    }

    boolean[] inStack = new boolean[256];
    for (char c : s.toCharArray()) {
        // 每遍历过一个字符，都将对应的计数减一
        count[c]--;
	//加入之前先判断有没有已经存在了
        if (inStack[c]) continue;

        while (!stk.isEmpty() && stk.peek() > c) {
            // 若之后不存在栈顶元素了，则停止 pop
            if (count[stk.peek()] == 0) {
                break;
            }
            // 若之后还有，则可以 pop
            inStack[stk.pop()] = false;
        }
        stk.push(c);
        inStack[c] = true;
    }

    StringBuilder sb = new StringBuilder();
    while (!stk.empty()) {
        sb.append(stk.pop());
    }
    return sb.reverse().toString();
}

作者：labuladong
链接：https://leetcode-cn.com/problems/remove-duplicate-letters/solution/you-qian-ru-shen-dan-diao-zhan-si-lu-qu-chu-zhong-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 129、[字符串相加](https://leetcode-cn.com/problems/add-strings/)

![image-20210422041007473](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422041008.png)

### 思路

#### （1）双指针

![image-20210422041101145](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422041101.png)

```java
class Solution {
    public String addStrings(String num1, String num2) {
        StringBuilder res = new StringBuilder("");
        int i = num1.length() - 1, j = num2.length() - 1, carry = 0;
        while(i >= 0 || j >= 0){
            int n1 = i >= 0 ? num1.charAt(i) - '0' : 0;
            int n2 = j >= 0 ? num2.charAt(j) - '0' : 0;
            int tmp = n1 + n2 + carry;
            carry = tmp / 10;
            res.append(tmp % 10);
            i--; j--;
        }
        if(carry == 1) res.append(1);
        return res.reverse().toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/add-strings/solution/add-strings-shuang-zhi-zhen-fa-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210422041357939](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422041358.png)

![image-20210422041413967](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422041414.png)

![img](https://pic.leetcode-cn.com/ae367453af71c81803a1e9beee90495a19a406eacbb33f532acdf241f5297fed-Picture1.png)

![img](https://pic.leetcode-cn.com/113d975b781eae7a19258478e65e0017cfba5328af70eadd6d69e608ea7b7c94-Picture2.png)

![img](https://pic.leetcode-cn.com/23ffeeedcf0c11dece507bfd801c4af14f7888c2474cb08e4278a3bf27528541-Picture7.png)

++++++++++++++++++++++++++![img](https://pic.leetcode-cn.com/921c2d55581204b62c7af8fcbadf2716656bb4b454b2641973f9a1286999a89b-Picture8.png)



## 130、[ 翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

![image-20210422044335653](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422044338.png)



### 思路

#### （1）双指针加库函数

![image-20210422044816697](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422044817.png)



![img](https://pic.leetcode-cn.com/b381201214fe7ffa97d8e23575fc26cc3914be6941219cafb90124d6bcca173a-Picture2.png)

![img](https://pic.leetcode-cn.com/2b3fe2e60404c47a53ae225438dce5f4e213ad981bcbc0b06e397ea8340045e5-Picture3.png)



![img](https://pic.leetcode-cn.com/ba89778b47a981029406ba7efb84ef955cb3064b9f3113ffaa9e586e33d22955-Picture4.png)

![img](https://pic.leetcode-cn.com/acdf041c8ee64e86159e0ba176cf14700ca26b3c6a90503a076e947e2e10d096-Picture9.png)

![img](https://pic.leetcode-cn.com/67d9a4d475b8a5043b8b0e16704bebc6fe35fa74bdf08e77a3101fa2e8d76394-Picture12.png)

![img](https://pic.leetcode-cn.com/2923a078002c1dd35e885376759b6846ee853e944d2376e7cfc114f9fdc033a6-Picture13.png)



![img](https://pic.leetcode-cn.com/a628e3eca6187523fc80b253868b06736c6d91b07dc330dc67314d04e328b9a8-Picture18.png)

```java
class Solution {
    public String reverseWords(String s) {
        s = s.trim(); // 删除首尾空格
        int j = s.length() - 1, i = j;
        StringBuilder res = new StringBuilder();
        while(i >= 0) {
            while(i >= 0 && s.charAt(i) != ' ') i--; // 搜索首个空格
            res.append(s.substring(i + 1, j + 1) + " "); // 添加单词，最后可能会多空格
            while(i >= 0 && s.charAt(i) == ' ') i--; // 跳过单词间空格
            j = i; // j 指向下个单词的尾字符
        }
        return res.toString().trim(); // 转化为字符串并返回
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/reverse-words-in-a-string/solution/151-fan-zhuan-zi-fu-chuan-li-de-dan-ci-shuang-zh-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210422045109757](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422045110.png)



#### （2）自行编写对应的函数



```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = trimSpaces(s);

        // 翻转字符串
        reverse(sb, 0, sb.length() - 1);

        // 翻转每个单词
        reverseEachWord(sb);

        return sb.toString();
    }

    public StringBuilder trimSpaces(String s) {
        int left = 0, right = s.length() - 1;
        // 去掉字符串开头的空白字符
        while (left <= right && s.charAt(left) == ' ') {
            ++left;
        }

        // 去掉字符串末尾的空白字符
        while (left <= right && s.charAt(right) == ' ') {
            --right;
        }

        // 将字符串间多余的空白字符去除
        StringBuilder sb = new StringBuilder();
        while (left <= right) {
            char c = s.charAt(left);

            if (c != ' ') {
                sb.append(c);
            } else if (sb.charAt(sb.length() - 1) != ' ') {
                sb.append(c);
            }

            ++left;
        }
        return sb;
    }

    public void reverse(StringBuilder sb, int left, int right) {
        while (left < right) {
            char tmp = sb.charAt(left);
            sb.setCharAt(left++, sb.charAt(right));
            sb.setCharAt(right--, tmp);
        }
    }

    public void reverseEachWord(StringBuilder sb) {
        int n = sb.length();
        int start = 0, end = 0;

        while (start < n) {
            // 循环至单词的末尾
            while (end < n && sb.charAt(end) != ' ') {
                ++end;
            }
            // 翻转单词
            reverse(sb, start, end - 1);
            // 更新start，去找下一个单词
            start = end + 1;
            ++end;
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/reverse-words-in-a-string/solution/fan-zhuan-zi-fu-chuan-li-de-dan-ci-by-leetcode-sol/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210422050018895](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422050020.png)





#### （3）递归

![image-20210422050305558](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422050306.png)

```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = new StringBuilder();
        getReverseWords(s, sb, 0, false);
        return sb.toString();
    }
    
    public void getReverseWords(String s, StringBuilder sb, int start, boolean flag) {

        // 跳过空格
        while (start < s.length() && s.charAt(start) == ' ') start ++;
        if (start == s.length()) return;

        // 获取单词，并递归
        int end = start;
        while (end < s.length() && s.charAt(end) != ' ') end ++;
        getReverseWords(s, sb, end, true);

        // 递归回来后，插入本位置对应单词
        sb.append(s.substring(start, end));

        // flag为true时，插入单词后再插入空格
        if (flag) sb.append(" ");
    }
}

作者：iame
链接：https://leetcode-cn.com/problems/reverse-words-in-a-string/solution/151-fan-zhuan-zi-fu-chuan-li-de-dan-ci-java-di-gui/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210422051206488](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210422051207.png)









# 剑指offer



## 131、[04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

![image-20210423032339135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423111852.png)

### 思路

#### （1）标志数/二叉搜索树

![image-20210423033057110](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423033058.png)

![image-20210423033110138](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423033110.png)

![img](https://pic.leetcode-cn.com/6a083897417b51e94ed84e3483d334078d851e691eb8655b45432372ecdea9d6-Picture2.png)

![img](https://pic.leetcode-cn.com/c301ea07f6081e95d06c07cc23fb0419e67ffdd92c2511201f72c3f86f18c928-Picture3.png)

![img](https://pic.leetcode-cn.com/3f1f6c8af23c89cd3179f486cfb932322ea4fa08ab707dc5e20b9adb243278e9-Picture4.png)

![img](https://pic.leetcode-cn.com/533c0eba70d25e5f7a1930186389a38feae15a91eea771fd388edd1eecc0b129-Picture5.png)

![img](https://pic.leetcode-cn.com/116704601a28972d17b32cc641485a1ab707930504a720160e121b092e9f7084-Picture6.png)

![img](https://pic.leetcode-cn.com/7db0cca850374644eff397880d5cdb3be17a3558a306162955a7ffb31bbf4e5c-Picture7.png)

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        int i = matrix.length - 1, j = 0;
        while(i >= 0 && j < matrix[0].length)
        {
            if(matrix[i][j] > target) i--;
            else if(matrix[i][j] < target) j++;
            else return true;
        }
        return false;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/solution/mian-shi-ti-04-er-wei-shu-zu-zhong-de-cha-zhao-zuo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210423033234583](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423033235.png)

![image-20210423033245016](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423033245.png)

## 132、[11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

![image-20210423033937555](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423033938.png)

### 思路

#### （1）二分法

![image-20210423041754755](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423041755.png)

![image-20210423041923110](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423041923.png)

![image-20210423042216782](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423042217.png)

![image-20210423042244578](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423042245.png)

![image-20210423042432624](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423042433.png)

![image-20210423043433205](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423043433.png)

```java
class Solution {
    public int minArray(int[] numbers) {
        int i = 0, j = numbers.length - 1;
        while (i < j) {
            int m = (i + j) / 2;
            if (numbers[m] > numbers[j]) i = m + 1;
            else if (numbers[m] < numbers[j]) j = m;
            else j--;
        }
        return numbers[i];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/mian-shi-ti-11-xuan-zhuan-shu-zu-de-zui-xiao-shu-3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210423043504264](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423043504.png)

![image-20210423043528539](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423043529.png)

![img](https://pic.leetcode-cn.com/1599404042-VzHrmU-Picture2.png)

![img](https://pic.leetcode-cn.com/1599404042-fNXpQJ-Picture3.png)

![image-20210423043844535](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423043845.png)

![img](https://pic.leetcode-cn.com/1599404042-sBLuCR-Picture5.png)

![img](https://pic.leetcode-cn.com/1599404042-lYmLFN-Picture6.png)

![img](https://pic.leetcode-cn.com/1599404042-HkRBZW-Picture7.png)

![img](https://pic.leetcode-cn.com/1599404366-eOwigV-Picture8.png)

![img](https://pic.leetcode-cn.com/1599404366-ngPDoD-Picture9.png)

![img](https://pic.leetcode-cn.com/1599404438-qzDKAI-Picture10.png)





#### （2）二分转线性

![image-20210423044447069](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423044448.png)

```java
class Solution {
    public int minArray(int[] numbers) {
        int i = 0, j = numbers.length - 1;
        while (i < j) {
            int m = (i + j) / 2;
            if (numbers[m] > numbers[j]) i = m + 1;
            else if (numbers[m] < numbers[j]) j = m;
            else {
                int x = i;
                for(int k = i + 1; k < j; k++) {
                    if(numbers[k] < numbers[x]) x = k;
                }
                return numbers[x];
            }
        }
        return numbers[i];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/mian-shi-ti-11-xuan-zhuan-shu-zu-de-zui-xiao-shu-3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 133、[10- II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

![image-20210423045857182](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423045857.png)

### 思路

#### （1）递归法**超时**

![image-20210423053919133](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423053919.png)

![image-20210423054351077](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423054351.png)

```java
class Solution {
    public int numWays(int n) {
        return Ways(n);
    }
    private int Ways(int n){
        if(n <= 1){
            return 1;
        }
        if( n == 2){
            return 2;
        }
        return (Ways(n-1) % 1000000007) + (Ways(n-2) % 1000000007);
    }
}

作者：PaperTigers
链接：https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/solution/jian-zhi-offerer-shua-javacong-di-gui-da-rp2h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）**备忘录法**

![image-20210423054451929](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423054453.png)

```java
class Solution {
    public int numWays(int n) {
        int[] memo = new int[101];
        // 备忘录初始化
        memo[0] = 1;
        memo[1] = 1;
        memo[2] = 2;
        return Ways(memo, n);
    }
    private int Ways(int[] memo, int n){
        // 如果备忘录中已经存在值，直接返回已经记录的值
        if(memo[n] !=0){
            return memo[n];
        }
        memo[n] = (Ways(memo, n-1) + Ways(memo, n-2)) % 1000000007;
        return memo[n];
        
    }
}

作者：PaperTigers
链接：https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/solution/jian-zhi-offerer-shua-javacong-di-gui-da-rp2h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）**动态规划**

![image-20210423054556017](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423054556.png)

###### 复杂度分析

![image-20210423054912015](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423054912.png)

时间复杂度O(n)，空间复杂度O(n)

#### （4）**动态规划的优化**

![image-20210423055046385](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423055047.png)

```java
public class Solution {
    public int numWays(int n) {
        if (n == 0 || n == 1) {
            return 1;
        }
        // 两个变量表示前两项
        int pre = 1, cur = 2;
        for (int i = 3; i <= n; i++) {
            int tmp = (pre + cur) % 1000_000_007;
            // 更新变量，以便继续求解
            pre = cur;
            cur = tmp;
        }
        return cur;
    }
}

作者：PaperTigers
链接：https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/solution/jian-zhi-offerer-shua-javacong-di-gui-da-rp2h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

时间复杂度O(n)，空间复杂度O(1)

![image-20210423055321545](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423055322.png)

## 134、[10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

![image-20210423055417929](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423055418.png)

### 思路

#### （1）递归超时

![image-20210423062152419](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423062153.png)

#### （2）递归不超时

![image-20210423062351935](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423062352.png)

![image-20210423063249509](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423063250.png)

```java
int constant = 1000000007;

public int fib(int n) {
    return fib(n, new HashMap());
}

public int fib(int n, Map<Integer, Integer> map) {
    if (n < 2)
        return n;
    if (map.containsKey(n))
        return map.get(n);
    int first = fib(n - 1, map) % constant;
    map.put(n - 1, first);
    int second = fib(n - 2, map) % constant;
    map.put(n - 2, second);
    int res = (first + second) % constant;
    map.put(n, res);
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/di-gui-he-fei-di-gui-liang-chong-fang-shi-du-ji-ba/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）非递归

![image-20210423063326067](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423063326.png)

```java
public int fib(int n) {
    int constant = 1000000007;
    int first = 0;
    int second = 1;
    while (n-- > 0) {
        int temp = first + second;
        first = second % constant;
        second = temp % constant;
    }
    return first;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/di-gui-he-fei-di-gui-liang-chong-fang-shi-du-ji-ba/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
    
```

```java
class Solution {
    public int fib(int n) {
        int a = 0, b = 1, sum;
        //这里多循环了一次，所以是a指向了f(n)
        for(int i = 0; i < n; i++){
            sum = (a + b) % 1000000007;
            //第i项
            a = b;
            //后面i+1项
            b = sum;
        }
        //a，b代表相邻两个斐波那契数；a是前一个，b是后一个。经过一次迭代后a代表fib(1)的值，两次		 //迭代后a代表fib(2)的值，因此经过n次迭代a代表的是fib(n)的值，因此返回a才是正确结果~
        return a;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/mian-shi-ti-10-i-fei-bo-na-qi-shu-lie-dong-tai-gui/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210423110813854](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423111903.png)

#### （4）记忆化搜索

```java
public int fib(int n) {
    int[] memo = new int[n + 1];
    Arrays.fill(memo, -1);
    return dfs(n, memo);
}
// 时间复杂度：O(n)
private int dfs(int n, int[] memo) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    if (memo[n] != -1) {
        return memo[n];
    }
    int leftFib = dfs(n - 1, memo);
    int rightFib = dfs(n - 2, memo);

    memo[n] = (leftFib + rightFib) % 1000000007;;

    return memo[n];
}

作者：tangweiqun
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/dong-tai-gui-hua-de-ru-men-suan-fa-ti-ka-7wtf/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210423112121372](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423112122.png)

#### （5）动态规划

```java
// 动态规划的四个步骤
    public int fib(int n) {
        if (n <= 1) return n;
        // 1. 定义状态数组，dp[i] 表示的是数字 i 的斐波那契数
        int[] dp = new int[n + 1];

        // 2. 状态初始化
        dp[0] = 0;
        dp[1] = 1;

        // 3. 状态转移
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
            // 取模
            dp[i] %= 1000000007;
        }

        // 4. 返回最终需要的状态值
        return dp[n];
    }

作者：tangweiqun
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/dong-tai-gui-hua-de-ru-men-suan-fa-ti-ka-7wtf/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （6）优化版动态规划

```java
// 状态数组空间压缩
public int fib(int n) {
    if (n <= 1) return n;
    // 只存储前两个状态
    int prev = 0;
    int curr = 1;
    for (int i = 2; i <= n; i++) {
        int sum = (prev + curr) % 1000000007;;
        prev = curr;
        //返回和的这个cur
        curr = sum;
    }
    return curr;
}

作者：tangweiqun
链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/solution/dong-tai-gui-hua-de-ru-men-suan-fa-ti-ka-7wtf/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 135、[09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

![image-20210423114500315](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423114531.png)

### 思路

#### （1）双栈

![image-20210423114956026](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423114956.png)

![image-20210423115044092](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423115044.png)

![img](https://pic.leetcode-cn.com/ff3c5e71e479e64fc0a1cc07cb653e50da3ab614e920b39c8122c173d092ba19-Picture1.png)

![img](https://pic.leetcode-cn.com/77c5b8da759d243c7f4a19608483eef6eea4979d89e21a969d0dc6f017266be3-Picture2.png)

![img](https://pic.leetcode-cn.com/d7b6c80bdcbbc293f77ed3ba0faa1e58914def046f8e013c7bb24431611e3d23-Picture3.png)

![img](https://pic.leetcode-cn.com/20c76486e1cefc96b05d25e4b61ae685f8b3e33599bf4aabdc95a757fca33aa0-Picture4.png)

![img](https://pic.leetcode-cn.com/f937cdc432c8b835f626a4ad6a4bf3307d1e90b77c39d8b5426e6f6016c16360-Picture5.png)

![img](https://pic.leetcode-cn.com/95bcb4872f80596a74a5331c4658e388599e62d24c0e86c70673835e7747e87e-Picture6.png)

![img](https://pic.leetcode-cn.com/e57162b03d995cc8e550895ca2d08856f8f3035e24cf293219e232791eec468e-Picture7.png)

![img](https://pic.leetcode-cn.com/8401a33b62a40cefb1cd25712ae6c3fe49c8f7f5bfe77fa154675ea615b12ade-Picture8.png)

```java
class CQueue {
    LinkedList<Integer> A, B;
    public CQueue() {
        A = new LinkedList<Integer>();
        B = new LinkedList<Integer>();
    }
    public void appendTail(int value) {
        A.addLast(value);
    }
    public int deleteHead() {
        if(!B.isEmpty()) return B.removeLast();
        if(A.isEmpty()) return -1;
        while(!A.isEmpty())
            B.addLast(A.removeLast());
        return B.removeLast();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/solution/mian-shi-ti-09-yong-liang-ge-zhan-shi-xian-dui-l-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210423115226179](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423115226.png)

![image-20210423115916795](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423115917.png)

![fig1](https://assets.leetcode-cn.com/solution-static/jianzhi_09/jianzhi_9.gif)

## 136、[06. 从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

![image-20210423132711730](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423132712.png)

### 思路

#### （1）递归

![image-20210423133243528](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423133244.png)

![img](https://pic.leetcode-cn.com/694435ca220f61a874729d3be12ab594be0be530760caf5e25d71bf7a9f29b8b-Picture1.png)

![img](https://pic.leetcode-cn.com/4ea9eec2a6c504eb5444e693ad94ad5eaed26b215733eedf0a2c9a1ed3781390-Picture2.png)

![img](https://pic.leetcode-cn.com/3a81b73ee54d312ac8349ffedcc69a714f2cd814d70c1f3f0efbbe23f91cb9fc-Picture3.png)

![img](https://pic.leetcode-cn.com/d3e724a1e77b77bd9323c8a2606d5ec9464ef8b2c81a1d33a7af96655457a483-Picture4.png)

![img](https://pic.leetcode-cn.com/4dfa31e2cc8ccc65e43a2ac95e394de7bf234af927878054a53e6a441aa9eacc-Picture5.png)

![img](https://pic.leetcode-cn.com/a4b8bd414664b8688a411ef3753aa669594da340f20e53f62a57ac7ab7df3a3b-Picture6.png)

![img](https://pic.leetcode-cn.com/76178a4127e47bbe7fd6ca59121843d93a5e1e4aa7bcffb86062d925499f3dde-Picture7.png)

![img](https://pic.leetcode-cn.com/65b63e3fc8277c16c80a676aaa13f8110083a0e31d95c0ecc196f1c12d872767-Picture8.png)

![img](https://pic.leetcode-cn.com/363e04c2ae104ed0275d4fcd953d21e20ed1536a98902c5c83843c2c6cff0590-Picture9.png)

![img](https://pic.leetcode-cn.com/1066f15bd86d99e7998519f4e2ffee070401ec67102f6e3626a61811987d99b5-Picture10.png)

```java
class Solution {
    ArrayList<Integer> tmp = new ArrayList<Integer>();
    public int[] reversePrint(ListNode head) {
        recur(head);
        int[] res = new int[tmp.size()];
        for(int i = 0; i < res.length; i++)
            res[i] = tmp.get(i);
        return res;
    }
    void recur(ListNode head) {
        if(head == null) return;
        //走到链表尾巴
        recur(head.next);
        //再回溯一个个加到list中
        tmp.add(head.val);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/solution/mian-shi-ti-06-cong-wei-dao-tou-da-yin-lian-biao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210423134235701](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423134236.png)



###### 复杂度分析

![image-20210423133436244](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423133436.png)

#### （2）辅助栈法

![image-20210423133545293](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423133546.png)

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        LinkedList<Integer> stack = new LinkedList<Integer>();
        while(head != null) {
            stack.addLast(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        for(int i = 0; i < res.length; i++)
            res[i] = stack.removeLast();
    return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/solution/mian-shi-ti-06-cong-wei-dao-tou-da-yin-lian-biao-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 137、[Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

![image-20210423171612830](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423171613.png)

### 思路

#### （1）分治算法

![image-20210423171843431](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423171844.png)

![image-20210423173204324](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423173206.png)

![img](https://pic.leetcode-cn.com/1603644245-DwefAv-Picture2.png)

![img](https://pic.leetcode-cn.com/1603644377-aJTwyJ-Picture3.png)

![img](https://pic.leetcode-cn.com/1603644377-rGvUqA-Picture4.png)

![img](https://pic.leetcode-cn.com/1603644245-OrFteB-Picture5.png)

![img](https://pic.leetcode-cn.com/1603644245-RBtYMS-Picture6.png)

![image-20210423173335580](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423173336.png)

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423173336.png)

![img](https://pic.leetcode-cn.com/1603644245-leDZaF-Picture8.png)

![img](https://pic.leetcode-cn.com/1603644245-KtWHlj-Picture9.png)

![img](https://pic.leetcode-cn.com/1603644245-tYOIfd-Picture10.png)

![img](https://pic.leetcode-cn.com/1603644245-IAznrm-Picture11.png)

![img](https://pic.leetcode-cn.com/1603644245-oLIgwn-Picture12.png)



![image-20210423174631274](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423174633.png)

```java
class Solution {
    int[] preorder;
    HashMap<Integer, Integer> dic = new HashMap<>();
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        for(int i = 0; i < inorder.length; i++)
            dic.put(inorder[i], i);
        return recur(0, 0, inorder.length - 1);
    }
    TreeNode recur(int root, int left, int right) {
        // 相等的话就是自己
        if(left > right) return null;                          // 递归终止
        TreeNode node = new TreeNode(preorder[root]);          // 建立根节点
        
        //获取在中序遍历中根节点所在索引，以方便获取左子树的数量
        int i = dic.get(preorder[root]);                       // 划分根节点、左子树、右子树
        
           //左子树的根的索引为先序中的根节点+1 
        //递归左子树的左边界为原来的中序in_left
        //递归右子树的右边界为中序中的根节点索引-1
        node.left = recur(root + 1, left, i - 1);              // 开启左子树递归
        
         //右子树的根的索引为先序中的 当前根位置 + 左子树的数量 + 1
        //递归右子树的左边界为中序中当前根节点+1
        //递归右子树的有边界为中序中原来右子树的边界
        node.right = recur(root + i - left + 1, i + 1, right); // 开启右子树递归
        return node;                                           // 回溯返回根节点
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/solution/mian-shi-ti-07-zhong-jian-er-cha-shu-di-gui-fa-qin/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210423191242987](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423191243.png)

```java
import java.util.HashMap;
import java.util.Map;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
    }
}


public class Solution {

    // 使用全局变量是为了让递归方法少传一些参数，不一定非要这么做

    private Map<Integer, Integer> reverses;
    private int[] preorder;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int preLen = preorder.length;
        int inLen = inorder.length;

        // 可以不做判断，因为题目中给出的数据都是有效的
        if (preLen != inLen) {
            return null;
        }

        this.preorder = preorder;

        // 以空间换时间，否则，找根结点在中序遍历中的位置需要遍历
        reverses = new HashMap<>(inLen);
        for (int i = 0; i < inLen; i++) {
            reverses.put(inorder[i], i);
        }

        return buildTree(0, preLen - 1, 0, inLen - 1);
    }

    /**
     * 根据前序遍历数组的 [preL, preR] 和 中序遍历数组的 [inL, inR] 重新组建二叉树
     *
     * @param preL 前序遍历数组的区间左端点
     * @param preR 前序遍历数组的区间右端点
     * @param inL  中序遍历数组的区间左端点
     * @param inR  中序遍历数组的区间右端点
     * @return 构建的新二叉树的根结点
     */
    private TreeNode buildTree(int preL, int preR,
                               int inL, int inR) {
        if (preL > preR || inL > inR) {
            return null;
        }
        // 构建的新二叉树的根结点一定是前序遍历数组的第 1 个元素
        int pivot = preorder[preL];
        TreeNode root = new TreeNode(pivot);

        int pivotIndex = reverses.get(pivot);

        // 这一步得画草稿，计算边界的取值，必要时需要解方程，并不难
        root.left = buildTree(preL + 1, preL + (pivotIndex - inL), inL, pivotIndex - 1);
        root.right = buildTree(preL + (pivotIndex - inL) + 1, preR, pivotIndex + 1, inR);
        return root;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/solution/er-cha-shu-de-qian-xu-bian-li-fen-zhi-si-xiang-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210423173457281](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423173457.png)



## 138、 [12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

![image-20210423193928231](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423193929.png)

### 思路

#### （1）DFS+剪枝

![image-20210423194106479](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423194107.png)

![image-20210423194149676](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423194150.png)

![img](https://pic.leetcode-cn.com/1600793567-fPZPYj-Picture1.png)

![img](https://pic.leetcode-cn.com/1600793567-hVwomN-Picture2.png)

![img](https://pic.leetcode-cn.com/1600793567-yoOQer-Picture3.png)

![img](https://pic.leetcode-cn.com/1600793567-qyZcNF-Picture4.png)

![img](https://pic.leetcode-cn.com/1600793567-pvxYVP-Picture5.png)

![img](https://pic.leetcode-cn.com/1600793567-izfBtb-Picture6.png)

![img](https://pic.leetcode-cn.com/1600793567-bwnSPQ-Picture7.png)

![img](https://pic.leetcode-cn.com/1600793567-pnRRIE-Picture8.png)

![img](https://pic.leetcode-cn.com/1600793567-yQNohz-Picture9.png)

![img](https://pic.leetcode-cn.com/1600793567-zLOKmi-Picture10.png)

![img](https://pic.leetcode-cn.com/1600793567-neMJnx-Picture11.png)

![img](https://pic.leetcode-cn.com/1600793567-dyuEzg-Picture12.png)

![img](https://pic.leetcode-cn.com/1600793567-lnVDIT-Picture13.png)

![img](https://pic.leetcode-cn.com/1600793567-tLXXgg-Picture14.png)

![img](https://pic.leetcode-cn.com/1600793567-VwCJGV-Picture15.png)

![img](https://pic.leetcode-cn.com/1600793567-aNgVtM-Picture16.png)

![img](https://pic.leetcode-cn.com/1600793567-EcdfJE-Picture17.png)

![img](https://pic.leetcode-cn.com/1600793793-PrBQdk-Picture18.png)

![image-20210423194626241](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423194626.png)

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for(int i = 0; i < board.length; i++) {
            for(int j = 0; j < board[0].length; j++) {
                if(dfs(board, words, i, j, 0)) return true;
            }
        }
        return false;
    }
    boolean dfs(char[][] board, char[] word, int i, int j, int k) {
        if(i >= board.length || i < 0 || j >= board[0].length || j < 0 || board[i][j] != word[k]) return false;
        if(k == word.length - 1) return true;
        board[i][j] = '\0';
        boolean res = dfs(board, word, i + 1, j, k + 1) || dfs(board, word, i - 1, j, k + 1) || 
                      dfs(board, word, i, j + 1, k + 1) || dfs(board, word, i , j - 1, k + 1);
        board[i][j] = word[k];
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/solution/mian-shi-ti-12-ju-zhen-zhong-de-lu-jing-shen-du-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210423194709834](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210423194710.png)



## 139、[13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

![image-20210424025341821](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424025344.png)

### 思路

#### （1）DFS回溯

![image-20210424032305798](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424032306.png)

![img](https://pic.leetcode-cn.com/6c80ef7224efa9a8f3589afdc83681fa50b2b231ad5f188ff2e5a92d4a6db47c-%E5%B9%BB%E7%81%AF%E7%89%871.JPG)

![img](https://pic.leetcode-cn.com/a5addecac64254ee7f899819d5c4e65117b786c747975de1d2d9cd2f309aab92-%E5%B9%BB%E7%81%AF%E7%89%872.JPG)

![img](https://pic.leetcode-cn.com/7eab5519c96fef9d4b06a413d9ad4ab18dc47012c0db0b9b60947f60229bb8e1-%E5%B9%BB%E7%81%AF%E7%89%873.JPG)

![img](https://pic.leetcode-cn.com/2019c08d97a5868606aa510808d9b1c147e484f95e3ee90819dd118387dcb4e4-%E5%B9%BB%E7%81%AF%E7%89%8710.JPG)

![img](https://pic.leetcode-cn.com/beecfe15fc0274544927c091f87960a8348279007a95afda7e4f2fdca0d5d41e-%E5%B9%BB%E7%81%AF%E7%89%8715.JPG)

![img](https://pic.leetcode-cn.com/75deefae1c4f7bbeb82c8ee1d61c3726ca18f26018d6e8ffdbcb24b20a79a2bf-%E5%B9%BB%E7%81%AF%E7%89%8718.JPG)

![img](https://pic.leetcode-cn.com/e2a18a98608616eb5ba81b853854a2e51267ef8f20a6ed1f6bf6e2bf4a259283-%E5%B9%BB%E7%81%AF%E7%89%8719.JPG)

![image-20210424032528597](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424032529.png)

![image-20210424033139710](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424033140.png)

![img](https://pic.leetcode-cn.com/1603026306-jCBpqd-Picture2.png)

![img](https://pic.leetcode-cn.com/1603026306-aEEvfM-Picture3.png)

![img](https://pic.leetcode-cn.com/1603026306-daxIuh-Picture4.png)

![img](https://pic.leetcode-cn.com/1603026306-HAMNPH-Picture5.png)

![img](https://pic.leetcode-cn.com/1603026306-IUKeMt-Picture6.png)

![img](https://pic.leetcode-cn.com/1603026306-YxlxXI-Picture7.png)

![img](https://pic.leetcode-cn.com/1603026306-JLhUWz-Picture8.png)

![image-20210424033352966](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424033353.png)

![image-20210424033446955](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424033447.png)

![img](https://pic.leetcode-cn.com/1603024999-URYkbB-Picture10.png)

![img](https://pic.leetcode-cn.com/1603024999-XyKkZo-Picture11.png)

![img](https://pic.leetcode-cn.com/1603024999-eQXMnv-Picture12.png)

![img](https://pic.leetcode-cn.com/1603024999-mcujIe-Picture13.png)

![img](https://pic.leetcode-cn.com/1603024999-dXOdEJ-Picture14.png)

![image-20210424033914858](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424033915.png)

![img](https://pic.leetcode-cn.com/1603024999-krgMMb-Picture16.png)

![image-20210424034019139](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424034019.png)

![img](https://pic.leetcode-cn.com/1603024999-jYYavg-Picture18.png)

![img](https://pic.leetcode-cn.com/1603024999-RRZCbl-Picture19.png)

![img](https://pic.leetcode-cn.com/1603024999-HBTzMV-Picture20.png)

![image-20210424034209182](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424034209.png)

![image-20210424034448630](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424034449.png)



```java
class Solution {
    //直接一个参数传递
    int m, n, k;
    boolean[][] visited;
    public int movingCount(int m, int n, int k) {
        this.m = m; this.n = n; this.k = k;
        this.visited = new boolean[m][n];
        //这里不用双重for，最后的结果返回的就是总的可达数量
        return dfs(0, 0, 0, 0);
    }
    public int dfs(int i, int j, int si, int sj) {
        if(i >= m || j >= n || k < si + sj || visited[i][j]) return 0;
        visited[i][j] = true;
        return 1 + dfs(i + 1, j, (i + 1) % 10 != 0 ? si + 1 : si - 8, sj) + dfs(i, j + 1, si, (j + 1) % 10 != 0 ? sj + 1 : sj - 8);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/mian-shi-ti-13-ji-qi-ren-de-yun-dong-fan-wei-dfs-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210424034243547](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424034244.png)

![image-20210424035544278](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424035544.png)

#### （2）BFS

![image-20210424044217155](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424044218.png)

![img](https://pic.leetcode-cn.com/1603024999-EKiknO-Picture22.png)

![img](https://pic.leetcode-cn.com/1603024999-hEnxrs-Picture23.png)

![img](https://pic.leetcode-cn.com/1603024999-goUtpL-Picture24.png)

![image-20210424044546272](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424044547.png)

![img](https://pic.leetcode-cn.com/1603024999-bQyxbG-Picture26.png)

![img](https://pic.leetcode-cn.com/1603024999-tjBCbi-Picture27.png)

![img](https://pic.leetcode-cn.com/1603024999-MBLwqu-Picture28.png)

![img](https://pic.leetcode-cn.com/1603024999-NYKszu-Picture29.png)

![img](https://pic.leetcode-cn.com/1603024999-aMYSTi-Picture30.png)

![image-20210424045032974](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424045033.png)

```java
class Solution {
    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];
        int res = 0;
        Queue<int[]> queue= new LinkedList<int[]>();
        queue.add(new int[] { 0, 0, 0, 0 });
        while(queue.size() > 0) {
            int[] x = queue.poll();
            int i = x[0], j = x[1], si = x[2], sj = x[3];
            if(i >= m || j >= n || k < si + sj || visited[i][j]) continue;
            visited[i][j] = true;
            //统计visited中为true的个数，因为有些元素还是默认的false
            res ++;
            queue.add(new int[] { i + 1, j, (i + 1) % 10 != 0 ? si + 1 : si - 8, sj });
            queue.add(new int[] { i, j + 1, si, (j + 1) % 10 != 0 ? sj + 1 : sj - 8 });
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/mian-shi-ti-13-ji-qi-ren-de-yun-dong-fan-wei-dfs-b/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210424045833951](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424045834.png)

![image-20210424045847123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424045847.png)

#### （3）DFS另一种写法



```java
public int movingCount(int m, int n, int k) {
    //临时变量visited记录格子是否被访问过
    boolean[][] visited = new boolean[m][n];
    return dfs(0, 0, m, n, k, visited);
}

public int dfs(int i, int j, int m, int n, int k, boolean[][] visited) {
    //i >= m || j >= n是边界条件的判断，k < sum(i, j)判断当前格子坐标是否
    // 满足条件，visited[i][j]判断这个格子是否被访问过
    if (i >= m || j >= n || k < sum(i, j) || visited[i][j])
        return 0;
    //标注这个格子被访问过
    visited[i][j] = true;
    //沿着当前格子的右边和下边继续访问
    return 1 + dfs(i + 1, j, m, n, k, visited) + dfs(i, j + 1, m, n, k, visited);
}

//计算两个坐标数字的和
private int sum(int i, int j) {
    int sum = 0;
    while (i != 0) {
        sum += i % 10;
        i /= 10;
    }
    while (j != 0) {
        sum += j % 10;
        j /= 10;
    }
    return sum;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/dfshe-bfsliang-chong-jie-jue-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
（）著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （4）BFS的另外的写法

```java
public int movingCount(int m, int n, int k) {
    //临时变量visited记录格子是否被访问过
    boolean[][] visited = new boolean[m][n];
    int res = 0;
    //创建一个队列，保存的是访问到的格子坐标，是个二维数组
    Queue<int[]> queue = new LinkedList<>();
    //从左上角坐标[0,0]点开始访问，add方法表示把坐标
    // 点加入到队列的队尾
    queue.add(new int[]{0, 0});
    while (queue.size() > 0) {
        //这里的poll()函数表示的是移除队列头部元素，因为队列
        // 是先进先出，从尾部添加，从头部移除
        int[] x = queue.poll();
        int i = x[0], j = x[1];
        //i >= m || j >= n是边界条件的判断，k < sum(i, j)判断当前格子坐标是否
        // 满足条件，visited[i][j]判断这个格子是否被访问过
        if (i >= m || j >= n || k < sum(i, j) || visited[i][j])
            continue;
        //标注这个格子被访问过
        visited[i][j] = true;
        res++;
        //把当前格子右边格子的坐标加入到队列中
        queue.add(new int[]{i + 1, j});
        //把当前格子下边格子的坐标加入到队列中
        queue.add(new int[]{i, j + 1});
    }
    return res;
}

//计算两个坐标数字的和
private int sum(int i, int j) {
    int sum = 0;
    while (i != 0) {
        sum += i % 10;
        i /= 10;
    }
    while (j != 0) {
        sum += j % 10;
        j /= 10;
    }
    return sum;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/solution/dfshe-bfsliang-chong-jie-jue-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 140、[14- I. 剪绳子](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

![image-20210424050711327](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424050712.png)

### 思路

#### （1）数学推导贪心算法

![image-20210424122901617](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424122901.png)

![image-20210424051549109](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424051549.png)

![image-20210424051607015](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424051607.png)

![image-20210424051825013](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424051825.png)

###### 代码

```java
class Solution {
    public int cuttingRope(int n) {
        if(n <= 3) return n - 1;
        int a = n / 3, b = n % 3;
        if(b == 0) return (int)Math.pow(3, a);
        if(b == 1) return (int)Math.pow(3, a - 1) * 4;
        return (int)Math.pow(3, a) * 2;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/mian-shi-ti-14-i-jian-sheng-zi-tan-xin-si-xiang-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210424051853495](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424051854.png)

![image-20210424052440672](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424052441.png)

#### （2）动态规划

![image-20210424122240823](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424122241.png)

```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];
        for (int i = 2; i <= n; i++) {
            //由于要拆成两段，所以j<i
            for (int j = 1; j < i; j++) {
                //这里的dp[i]保留了上一轮j所求得的dp[i]的最大值
                dp[i]= Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));
            }
        }
        return dp[n];
    }
}

作者：Sophia_fez
链接：https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/dong-tai-gui-hua-shu-xue-by-sophia_fez/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210424122654465](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424122654.png)

![image-20210424122707573](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424122707.png)

#### （3）递归超时

![image-20210424124144848](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424124145.png)

```java
class Solution {
    public int cuttingRope(int n) {
        // 特殊情况
        if(n == 2){
            return 1;
        }
        int res = 0;
        for(int i = 2; i < n; i++){
            res = Math.max(res, Math.max(i * cuttingRope(n-i), i * (n-i)));
        }
        return res;
    }
    
}

作者：PaperTigers
链接：https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/jian-zhi-offerer-shua-java-di-gui-bei-wa-1pax/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （4）**递归 备忘录法**

![image-20210424124855360](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424124855.png)

###### 复杂度分析

![image-20210424125113128](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424125113.png)

## 141、[ 14- II. 剪绳子 II](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/)

![image-20210424141642562](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424141642.png)

### 思路

#### （1）贪心算法

![image-20210424143904072](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424143905.png)

![image-20210424144844659](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424144845.png)

###### 代码

```java
    public int cuttingRope(int n) {
        if (n == 2 || n == 3)
            return n - 1;
        int constant = 1000000007;
        long res = 1;
        while (n > 4) {
            //如果n大于4，我们不停的让他减去3
            n = n - 3;
            //计算每段的乘积
            res = res * 3 % constant;
        }
        // 最后n的值只有可能是：2、3、4。而2、3、4能得到的最大乘积恰恰就是自身值
        // 因为2、3不需要再剪了（剪了反而变小）；4剪成2x2是最大的，2x2恰巧等于4
        return (int) ((res * n) % constant);
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/shu-xue-zhi-shi-jie-jue-you-gong-shi-tui-dao-guo-c/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210424145440760](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424145441.png)



#### （2）快速幂解贪心

```java
class Solution {

    private int mod = (int)1e9 + 7;

    public int cuttingRope(int n) {
        if(n < 4){
            return n-1;
        }
        int cnt3 = n / 3;
        if(n % 3 == 0){
            return (int)pow(3, cnt3);
        } else if(n % 3 == 1){
            return (int)((pow(3, cnt3 - 1) * 4) % mod);
        } else {
            return (int)((pow(3, cnt3) * 2) % mod);
        }
    }

    private long pow(long base, int num){
        long res = 1;
        while(num > 0){
            if((num & 1) == 1){
                res *= base;
                res %= mod;
            }
            base *= base;
            base %= mod;
            num >>= 1;
        }
        return res;
    }
}

作者：HenryLee4
链接：https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/javatan-xin-si-lu-jiang-jie-by-henrylee4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 142、[15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

![image-20210424151435909](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424151436.png)

### 思路

#### （1）位运算



![image-20210424151850435](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424151850.png)

###### 代码

```java
public class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        //这里不能大于0，因为最高位可能是1，是负数，但是移动到最后肯定是等于0的
        while(n != 0) {
            res += n & 1;
            n >>>= 1;
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/solution/mian-shi-ti-15-er-jin-zhi-zhong-1de-ge-shu-wei-yun/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210424151914280](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424151914.png)

![image-20210424151939764](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424151940.png)

#### （2）巧用 n \& (n - 1)

![image-20210424152609115](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424152609.png)

###### 二进制减法详见开头前置知识

###### 代码

```java
public class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        while(n != 0) {
            res++;
            n &= n - 1;
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/solution/mian-shi-ti-15-er-jin-zhi-zhong-1de-ge-shu-wei-yun/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210424154227299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424154228.png)

![image-20210424154329681](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424154330.png)



## 143、[16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

![image-20210424154614237](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424154615.png)

### 思路

#### （1）非递归

![image-20210424170442656](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424170443.png)

###### 二分推导

![image-20210424183431476](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424183438.png)

![image-20210424190929371](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424190932.png)

```java
class Solution {
    public double myPow(double x, int n) {
        if(x == 0) return 0;
        long b = n;
        double res = 1.0;
        if(b < 0) {
            x = 1 / x;
            b = -b;
        }
        while(b > 0) {
            if((b & 1) == 1) res *= x;
            //其实就是底数x不断相乘变大，指数不断二分减小，最后当指数变成0后，x就变成结果了
            //有点像分治，不断地转换成求2次方的值
            x *= x;
            b >>= 1;
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/solution/mian-shi-ti-16-shu-zhi-de-zheng-shu-ci-fang-kuai-s/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210424191624468](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424191625.png)

![image-20210424191718417](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424191719.png)

#### （2）递归

![image-20210424193028919](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424193030.png)

```java
public class Solution {

    public double myPow(double x, int n) {
        // 特判，也可以认为是递归终止条件
        long N = n;
        if (N < 0) {
            return 1 / myPow(x, -N);
        }
        return myPow(x, N);
    }

    private double myPow(double x, long n) {
        if (n == 0) {
            return 1;
        }

        if (x == 1) {
            return 1;
        }

        // 根据指数是奇数还是偶数进行分类讨论
        // 使用位运算的 与 运算符代替了求余数运算

        if ((n % 2) == 0) {
            // 分治思想：分
            double square = myPow(x, n / 2);
            // 分治思想：合，下面同理
            return square * square;
        } else {
            // 是奇数的时候
            double square = myPow(x, (n - 1) / 2);
            return square * square * x;
        }
    }

}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/solution/di-gui-xie-fa-fen-zhi-si-xiang-yu-fei-di-gui-xie-f/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 144、[17. 打印从1到最大的n位数](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

![image-20210424195217940](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424195218.png)



### 思路

#### （1）分治和全排列

![image-20210424195352133](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210424195353.png)

![image-20210425025103788](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425025105.png)

![image-20210425025534716](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425025535.png)

![image-20210425025557426](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425025557.png)

![image-20210425025618395](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425025618.png)

![image-20210425025659093](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425025700.png)

```java
class Solution {
    int[] res;
    int nine = 0, count = 0, start, n;
    char[] num, loop = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};
    public int[] printNumbers(int n) {
        this.n = n;
        res = new int[(int)Math.pow(10, n) - 1];
        num = new char[n];
        start = n - 1;
        dfs(0);
        return res;
    }
    void dfs(int x) {
        if(x == n) {
            String s = String.valueOf(num).substring(start);
            if(!s.equals("0")) res[count++] = Integer.parseInt(s);
            if(n - start == nine) start--;
            return;
        }
        for(char i : loop) {
            if(i == '9') nine++;
            num[x] = i;
            dfs(x + 1);
        }
        nine--;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/solution/mian-shi-ti-17-da-yin-cong-1-dao-zui-da-de-n-wei-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）全排列2

![image-20210425031229770](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425031230.png)

![image-20210425031931238](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425031932.png)

###### 代码

```java
class Solution {
    int[] res;
    int count = 0;

    public int[] printNumbers(int n) {
        res = new int[(int)Math.pow(10, n) - 1];
        for(int digit = 1; digit < n + 1; digit++){
            for(char first = '1'; first <= '9'; first++){
                char[] num = new char[digit];
                num[0] = first;
                dfs(1, num, digit);
            }
        }
        return res;
    }

    private void dfs(int index, char[] num, int digit){
        if(index == digit){
            res[count++] = Integer.parseInt(String.valueOf(num));
            return;
        }
        for(char i = '0'; i <= '9'; i++){
            num[index] = i;
            dfs(index + 1, num, digit);
        }
    }
}

作者：edelweisskoko
链接：https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/solution/jian-zhi-offer-17-da-yin-cong-1dao-zui-d-ngm4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210425032005180](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425032006.png)



## 145、[20. 表示数值的字符串](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

![image-20210425032954940](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425032955.png)

### 思路

#### （1）判断

```java
class Solution {
    public boolean isNumber(String s) {
        if(s == null || s.length() == 0){
            return false;
        }
        //标记是否遇到相应情况
        boolean numSeen = false;
        boolean dotSeen = false;
        boolean eSeen = false;
        char[] str = s.trim().toCharArray();
        for(int i = 0;i < str.length; i++){
            if(str[i] >= '0' && str[i] <= '9'){
                numSeen = true;
            }else if(str[i] == '.'){
                //.之前不能出现.或者e
                if(dotSeen || eSeen){
                    return false;
                }
                dotSeen = true;
            }else if(str[i] == 'e' || str[i] == 'E'){
                //e之前不能出现e，必须出现数
                if(eSeen || !numSeen){
                    return false;
                }
                eSeen = true;
                //返回的是numSeen，所以后面有数字，才会重新置为true
                numSeen = false;//重置numSeen，排除123e或者123e+的情况,确保e之后也出现数
            }else if(str[i] == '-' || str[i] == '+'){
                //+-出现在0位置或者e/E的后面第一个位置才是合法的
                if(i != 0 && str[i-1] != 'e' && str[i-1] != 'E'){
                    return false;
                }
            }else{//其他不合法字符
                return false;
            }
        }
        return numSeen;
    }
}

作者：yangshyu6
链接：https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/solution/javaban-ben-ti-jie-luo-ji-qing-xi-by-yangshyu6/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210425055743691](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425055745.png)

#### （2）状态机

复杂的东西后面看



## 146、[21. 调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

![image-20210425061104369](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425061104.png)

### 思路

#### （1）首位双指针

```java
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0, j = nums.length - 1, tmp;
        while(i < j) {
            while(i < j && (nums[i] & 1) == 1) i++;
            while(i < j && (nums[j] & 1) == 0) j--;
            tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        return nums;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/solution/mian-shi-ti-21-diao-zheng-shu-zu-shun-xu-shi-qi-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image.png](https://pic.leetcode-cn.com/e98130e0572e082bad46240eb95860da6e6f962d07e4469c0d292085c8dc0ddb-image.png)



![image.png](https://pic.leetcode-cn.com/ff5c797d37bf18f141e00e1e0b440a2a58c585ae61a5af3b4e8640a4922b1915-image.png)



![image.png](https://pic.leetcode-cn.com/ff5c797d37bf18f141e00e1e0b440a2a58c585ae61a5af3b4e8640a4922b1915-image.png)



```java
    public int[] exchange(int[] nums) {
        if (nums == null || nums.length == 0)
            return nums;
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            while (left < right && (nums[left] & 1) == 1) {
                left++;
            }
            while (left < right && (nums[right] & 1) == 0) {
                right--;
            }
            if (left < right) {
                nums[left] ^= nums[right];
                nums[right] ^= nums[left];
                nums[left] ^= nums[right];
            }
        }
        return nums;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/solution/javayu-yan-de-3chong-shi-xian-da-an-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### （2）快慢指针

![image.png](https://pic.leetcode-cn.com/24a45bad7b93334e74c1a6443421bbcd14ba775a1e0f033856ca0a65e1e94273-image.png)

![image.png](https://pic.leetcode-cn.com/cc22805e375b239e14925110c80a70e5bbef948a92864e21764f08f9d7c07fb4-image.png)

```java
    public int[] exchange(int[] nums) {
        int slow = 0, fast = 0;
        while (fast < nums.length) {
            if ((nums[fast] & 1) == 1) {//奇数
                if (slow != fast) {
                    nums[slow] ^= nums[fast];
                    nums[fast] ^= nums[slow];
                    nums[slow] ^= nums[fast];
                }
                slow++;
            }
            fast++;
        }
        return nums;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/solution/javayu-yan-de-3chong-shi-xian-da-an-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210425062358851](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425062359.png)

## 147、[24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

![image-20210425063501347](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425063501.png)

### 思路

#### （1）双指针

![image-20210425063815652](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425063816.png)

![迭代.gif](https://pic.leetcode-cn.com/7d8712af4fbb870537607b1dd95d66c248eb178db4319919c32d9304ee85b602-%E8%BF%AD%E4%BB%A3.gif)

![image-20210425063834393](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425063834.png)

![img](https://pic.leetcode-cn.com/8f03740e93c8a0ecff54a57a25a710ab82574faf99b3efb8ddd2b6eea1d78d49-1.JPG)

![img](https://pic.leetcode-cn.com/aebc5e3ef0a6b942473a7c61f4de22e268cbfbe549ff1a6ad5d3eef7c667f875-2.JPG)

![img](https://pic.leetcode-cn.com/4c160db670c00f7467c1dae3b22ac117b84c0ba00690ca30708fabcdd5ff35b1-3.JPG)

![img](https://pic.leetcode-cn.com/95da97748c8a006b5b827d13154bd2202bed5c2b9d779bea6467473d16fd7d66-4.JPG)

![img](https://pic.leetcode-cn.com/bbc75893b050a8c482178029bd239bd75c75e6a58a4a16fb9270b13044e4ddf3-5.JPG)

![img](https://pic.leetcode-cn.com/9eda023eab4ecc76af90211626f77b695a4874b451084f8e2545717347815006-6.JPG)

![img](https://pic.leetcode-cn.com/1829f246e991d3e27aa3dce9b18ab88282a1bd34a5363969eb5730fe74cf8fbf-7.JPG)

![img](https://pic.leetcode-cn.com/3380338ba4b563c37199b07058d35fde9f9600d3112ef0b074e5986c2f4510e1-8.JPG)

![img](https://pic.leetcode-cn.com/db831df3a7db0fc405a4dc8293bfc92ba6bba6cb1719613a4e8ab7afdf2a53dd-9.JPG)

![img](https://pic.leetcode-cn.com/db7d85400d03237909fc904d52b71cf721dee82af47686756400fdf4d1f07493-10.JPG)

![img](https://pic.leetcode-cn.com/217edaea46af7111109f3dfb4846aad5e307935cec6e48ca42e0059d36792c25-11.JPG)

![img](https://pic.leetcode-cn.com/8c474aaee63b49b4cf981637039f65a41d70979a79fec22257576cfbcfb8c4c6-18.JPG)

![img](https://pic.leetcode-cn.com/5eaed6c73d6dec3c3dc7728fda178711ff1a6a558ddf8f2a1946adf22525a44c-19.JPG)

###### 代码

```java
class Solution {
	public ListNode reverseList(ListNode head) {
		//申请节点，pre和 cur，pre指向null
		ListNode pre = null;
		ListNode cur = head;
		ListNode tmp = null;
		while(cur!=null) {
			//记录当前节点的下一个节点
			tmp = cur.next;
			//然后将当前节点指向pre
			cur.next = pre;
			//pre和cur节点都前进一位
			pre = cur;
			cur = tmp;
		}
		return pre;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/solution/dong-hua-yan-shi-duo-chong-jie-fa-206-fan-zhuan-li/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210425065146161](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425065146.png)

#### （2）递归

![image-20210425064248839](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425064249.png)

![递归.gif](https://pic.leetcode-cn.com/dacd1bf55dec5c8b38d0904f26e472e2024fc8bee4ea46e3aa676f340ba1eb9d-%E9%80%92%E5%BD%92.gif)

![image-20210425064310226](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425064310.png)

![img](https://pic.leetcode-cn.com/bfa449ed16ecfc905f8ef8e9b049ed397b608f30dad84d37b2005e19797b1d49-%E5%B9%BB%E7%81%AF%E7%89%872.jpg)

![img](https://pic.leetcode-cn.com/c790acd5bd2ebf285ef2f687d76c9a48f2e32418acdb32909cc76cc069501ac7-%E5%B9%BB%E7%81%AF%E7%89%873.jpg)

![img](https://pic.leetcode-cn.com/d078fc5c224964b468210b205838ecac26efde1a0bd6fade6f3a758a87b82068-%E5%B9%BB%E7%81%AF%E7%89%874.jpg)

![img](https://pic.leetcode-cn.com/cd9c77c8c7873c4aa39bbb7a185b729151516021cee7009575c258fb82f77383-%E5%B9%BB%E7%81%AF%E7%89%875.jpg)

![img](https://pic.leetcode-cn.com/f89c9e095d414b8366dd7d490508ba9c99d6c93953b4429168af262169f18e83-%E5%B9%BB%E7%81%AF%E7%89%876.jpg)

![img](https://pic.leetcode-cn.com/485dbe9ad44ab7b05e01c46a1bc1718187a01f8a9fe8331f497e7011f9508b57-%E5%B9%BB%E7%81%AF%E7%89%877.jpg)

![image-20210425064555102](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425064555.png)

![img](https://pic.leetcode-cn.com/b167f5ee08eafab82d680cf782898b649dd2a4453cd122ad690e83e35c387a11-%E5%B9%BB%E7%81%AF%E7%89%879.jpg)

![img](https://pic.leetcode-cn.com/950f6edfb553cbeec65ce3c9679dd8d3401fd1837ad9eb16989217bf83f30e58-%E5%B9%BB%E7%81%AF%E7%89%8710.jpg)

![img](https://pic.leetcode-cn.com/dcd5bef9b8ff3de98e9533da7da3a8411643bbef9bc5e1a576085b4403197649-%E5%B9%BB%E7%81%AF%E7%89%8711.jpg)

![img](https://pic.leetcode-cn.com/1d6aaab3d9a42c20420fb6087e520ea05bd6d0789213f228481e55891b847b1e-%E5%B9%BB%E7%81%AF%E7%89%8712.jpg)

![img](https://pic.leetcode-cn.com/74bb9de62d3bb36a9c174d7702c7116974838b46eb96933e2921c054a34fa5d9-%E5%B9%BB%E7%81%AF%E7%89%8713.jpg)

![img](https://pic.leetcode-cn.com/e2bea51573eb5a763b5b9c0873887ced8932db95bc6bf4ff5befb363c8960984-%E5%B9%BB%E7%81%AF%E7%89%8714.jpg)

![img](https://pic.leetcode-cn.com/95db06ce86448f3265b0f3178baf7372915601e4b367fcde54ff5cf509532bc8-%E5%B9%BB%E7%81%AF%E7%89%8715.jpg)

![img](https://pic.leetcode-cn.com/fc96f0786c05a3a2ae0987757b4f569b3b18e59da0b5cb3afb063b9fc0ace069-%E5%B9%BB%E7%81%AF%E7%89%8716.jpg)

![img](https://pic.leetcode-cn.com/4e0975fd8d898364d24177684967fdf72d9476e64341c375ac2b5df7130b2f0a-%E5%B9%BB%E7%81%AF%E7%89%8723.jpg)

![img](https://pic.leetcode-cn.com/5625ed08dbaa84719f0b4b631fa6c4c7f08c1c5cf18646dbab2f65d410e99e8f-%E5%B9%BB%E7%81%AF%E7%89%8724.jpg)

###### 代码

```java
class Solution {
	public ListNode reverseList(ListNode head) {
		//递归终止条件是当前为空，或者下一个节点为空
		if(head==null || head.next==null) {
			return head;
		}
		//这里的cur就是最后一个节点
		ListNode cur = reverseList(head.next);
		//这里请配合动画演示理解
		//如果链表是 1->2->3->4->5，那么此时的cur就是5
		//而head是4，head的下一个是5，下下一个是空
		//所以head.next.next 就是5->4
		head.next.next = head;
		//防止链表循环，需要将head.next设置为空
		head.next = null;
		//每层递归函数都返回cur，也就是最后一个节点
		return cur;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/solution/dong-hua-yan-shi-duo-chong-jie-fa-206-fan-zhuan-li/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210425065044683](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425065045.png)



![image-20210425065159068](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425065159.png)

## 148、[合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

![image-20210425065256514](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210425065256.png)

### 思路

#### （1）伪头结点

![image-20210426040623413](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426040623.png)

![img](https://pic.leetcode-cn.com/61d5bf0954a4356fa15ad65c3e53daf4cf9ad2190ba7d69de930109626024c3f-Picture1.png)

![img](https://pic.leetcode-cn.com/fdac82aff201ed70bcbc69d5dcb4900a4034b400a9235d5fc85b74f1831d6603-Picture2.png)

![img](https://pic.leetcode-cn.com/38fa36d5ae325a0f27ee54e50483d5db74455bbf2ea5bc0c241b428d4a6b47c1-Picture3.png)

![image-20210426041038046](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426041038.png)

![img](https://pic.leetcode-cn.com/a5b0dffdd884e728641298dc67e290ec86ffe574f695c2500a082ebfdfc81232-Picture5.png)

![img](https://pic.leetcode-cn.com/915d84061f5374542070720fb667772ed45d80eeb7a25d6ac9b6baf519a547a9-Picture6.png)

![img](https://pic.leetcode-cn.com/65f63a6d5d396f92a00c1b1f88aade4f9068c1f0d182ce770c9adadd85e8bc07-Picture12.png)

![img](https://pic.leetcode-cn.com/4c200a2f801cc41a17925eb4d233d96466e7180c0cca65f6926fba12bdf71632-Picture13.png)

![img](https://pic.leetcode-cn.com/f8d4ee42e0ccb019745b1d0e73f21fbfd5989a0d5a56cdd7c9e9a97823545abe-Picture14.png)

![img](https://pic.leetcode-cn.com/426542cd8bbfe6ce716fab949a25e483daaef1950d691918509c05b6fbfd252c-Picture15.png)

![img](https://pic.leetcode-cn.com/6c7cf70eaea387f578622eb666984ba3b9cd451451c51326d1378ac234c09789-Picture16.png)

###### 代码

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        //有个cur指针初始是一起指向伪结点的，后面会移动
        ListNode dum = new ListNode(0), cur = dum;
        while(l1 != null && l2 != null) {
            if(l1.val < l2.val) {
                cur.next = l1;
                l1 = l1.next;
            }
            else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = l1 != null ? l1 : l2;
        return dum.next;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/solution/mian-shi-ti-25-he-bing-liang-ge-pai-xu-de-lian-b-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210426041923837](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426041924.png)

#### （2）递归

![image-20210426042742617](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426042743.png)

###### 代码

```java
    public ListNode mergeTwoLists(ListNode linked1, ListNode linked2) {
        //这两个判断要分开，不能用||合并，否则下面肯定会有空指针
        if (linked1 == null)
            return linked2;
        if (linked2 == null)
            return linked1;
        //上面的判断分开后，这里都不会有空指针了
        if (linked1.val < linked2.val) {
            linked1.next = mergeTwoLists(linked1.next, linked2);
            return linked1;
        } else {
            linked2.next = mergeTwoLists(linked1, linked2.next);
            return linked2;
        }
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/solution/javadai-ma-ji-bai-9927di-gui-he-fei-di-gui-tu-wen-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
    public ListNode mergeTwoLists(ListNode linked1, ListNode linked2) {
        //只要有一个为空，就返回另一个
        if (linked1 == null || linked2 == null)
            return linked2 == null ? linked1 : linked2;
        //把小的赋值给first
        ListNode first = (linked2.val < linked1.val) ? linked2 : linked1;
        first.next = mergeTwoLists(first.next, first == linked1 ? linked2 : linked1);
        return first;
    }


作者：sdwwld
链接：https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/solution/javadai-ma-ji-bai-9927di-gui-he-fei-di-gui-tu-wen-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210426043419476](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426043419.png)

![image-20210426043616051](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426043616.png)

## 149、[26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

![image-20210426051614012](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426051617.png)

### 思路

#### （1）递归

![image-20210426052548461](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426052549.png)

![img](https://pic.leetcode-cn.com/0e15908809807327aa87c748c4cf1d850eeb63cc13df94c87439cbaffb535005-Picture2.png)



![image-20210426052653432](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426052654.png)

![img](https://pic.leetcode-cn.com/76016cb01f7c051318761388eeadd774b334c6da3c5722754452fc82da87c04b-Picture4.png)

![img](https://pic.leetcode-cn.com/efe061c2ae8ae6c75b4f36bfd766d60471b4bc985cb330cdae5805043b19f440-Picture5.png)

![img](https://pic.leetcode-cn.com/987e1803a3600ef2b8e18320589e058e189888fb4d88389527f33499340f149a-Picture6.png)

![img](https://pic.leetcode-cn.com/3850986482cd6344935ac823e8b396b52f02ae196b573cea1e7ebecb97eb063c-Picture7.png)

![img](https://pic.leetcode-cn.com/24d94f730e0a7fc091ab321349c4524dd51828db1fa6a9c1b6455561fa27708e-Picture9.png)





![image-20210426051703643](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426051704.png)

![image-20210426051803559](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426051804.png)

![image-20210426051830912](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426051832.png)

###### 代码

```java
public boolean isSubStructure(TreeNode A, TreeNode B) {
    if (A == null || B == null)
        return false;
    //先从根节点判断B是不是A的子结构，如果不是在分别从左右两个子树判断，
    //只要有一个为true，就说明B是A的子结构
    return isSub(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
}

boolean isSub(TreeNode A, TreeNode B) {
    //这里如果B为空，说明B已经访问完了，确定是A的子结构
    if (B == null)
        return true;
    //如果B不为空A为空，或者这两个节点值不同，说明B树不是
    //A的子结构，直接返回false
    if (A == null || A.val != B.val)
        return false;
    //当前节点比较完之后还要继续判断左右子节点，是与不是或，因为从根节点开始比较的，所以左右子树		//都必须一模一样才行
    return isSub(A.left, B.left) && isSub(A.right, B.right);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/solution/di-gui-fang-shi-jie-jue-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210426053011995](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426053012.png)



## 150、[29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

![image-20210426054421450](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426054421.png)

### 思路

#### （1）模拟设定边界

![image-20210426060124221](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426060125.png)

![image-20210426060205915](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426060207.png)

![img](https://pic.leetcode-cn.com/4990446a88f72b7f03e6696bc6c718577d3190cf7559deb7d60af2c5857b2712-Picture7.png)

![img](https://pic.leetcode-cn.com/141da9868e0f4b45bb1d8a2ef8b55e2d1cb750b9d8f4f072db56e6f5aa269633-Picture2.png)

![img](https://pic.leetcode-cn.com/3d6fbb3b73ce3aa7899b7a7242a380efc481b4b5250132c91cc0e96e3c99d377-Picture3.png)

![img](https://pic.leetcode-cn.com/1f18872199acb988b1db4bacb69fc7b38c974c537750cc3c422152e58a3ee5f7-Picture4.png)

![img](https://pic.leetcode-cn.com/ad10b8cab62fdab0261302be2b75f4faceded16a278ddd85281687ab7a6be63e-Picture5.png)

![img](https://pic.leetcode-cn.com/f8cb8f27ce89678406f96f4c471f5aa7c69744899ffff03026c5f73c6806dfa7-Picture6.png)

###### 代码

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length == 0) return new int[0];
        int l = 0, r = matrix[0].length - 1, t = 0, b = matrix.length - 1, x = 0;
        int[] res = new int[(r + 1) * (b + 1)];
        while(true) {
            for(int i = l; i <= r; i++) res[x++] = matrix[t][i]; // left to right.
            //这里++ 就是收缩边界了，如果大于，说明++之前t、b已经相遇了
            if(++t > b) break;
            for(int i = t; i <= b; i++) res[x++] = matrix[i][r]; // top to bottom.
            if(l > --r) break;
            for(int i = r; i >= l; i--) res[x++] = matrix[b][i]; // right to left.
            if(t > --b) break;
            for(int i = b; i >= t; i--) res[x++] = matrix[i][l]; // bottom to top.
            if(++l > r) break;
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/solution/mian-shi-ti-29-shun-shi-zhen-da-yin-ju-zhen-she-di/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210426062522098](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426062522.png)

![image-20210426062539263](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210426062540.png)



## 151、[31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

![image-20210427051442916](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427051445.png)

### 思路

#### （1）辅助栈

![image-20210427051753642](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427051754.png)

![image-20210427052234093](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427052235.png)

![img](https://pic.leetcode-cn.com/05948472d4096747117036d4fd7430b817b84cb39946718127c912afe56378d5-Picture4.png)

![img](https://pic.leetcode-cn.com/c428090b921067f1c92d3c5e6f9abf45bda55e2ce44e99ecbc7db646251fe9fc-Picture6.png)

![img](https://pic.leetcode-cn.com/904b2334c1f1f7bc0c93f56e12c061aa69f34468a96c5bd9f50c06fa59e090ad-Picture7.png)

![img](https://pic.leetcode-cn.com/47909bda13373fab0c98972354bf942f19993776cb04e322a37c197198e0c705-Picture8.png)

![img](https://pic.leetcode-cn.com/83b13e44ad02b1046351707b7f4fd76bd1fa0d5e0413e8c0bc61fb9f890dad5e-Picture9.png)

![img](https://pic.leetcode-cn.com/e38264dc425209d2c0aaaab4d6050cd0a3d69d48c4c5877808e30788c752ff53-Picture10.png)

![img](https://pic.leetcode-cn.com/5c35507c10e3b7745ef769d6a879a28ee186be671daaf3ca75355153dd5543e4-Picture11.png)

![img](https://pic.leetcode-cn.com/958c96bbe4d77e3d0642bb9b13e1aaaeab5564e722b6516314b37a4f0958a209-Picture13.png)

![img](https://pic.leetcode-cn.com/9181035f31451f0ee53a5892f97e810577f01c4317c5bd9d2518ae335aae2f7e-Picture14.png)

###### 代码

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        //这里要设置一个索引，代表pop的位置
        int i = 0;
        for(int num : pushed) {
            stack.push(num); // num 入栈
            while(!stack.isEmpty() && stack.peek() == popped[i]) { // 循环判断与出栈
                stack.pop();
                i++;
            }
        }
        return stack.isEmpty();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/solution/mian-shi-ti-31-zhan-de-ya-ru-dan-chu-xu-lie-mo-n-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210427052509505](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427052510.png)

## 152、[33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

![image-20210427053953187](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427053954.png)

### 思路

#### （1）递归

![image-20210427055627062](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427055628.png)

![image-20210427055834199](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427055835.png)

![img](https://pic.leetcode-cn.com/b73df6ca25237169809aaef35b63879d5a03d2b34625fbd381d322679738251c-Picture3.png)

![img](https://pic.leetcode-cn.com/02386aac59ef81a812377eb3504a9231eee8e858e3c177be9d51a472817062dd-Picture2.png)

![image-20210427060431899](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427060432.png)

![img](https://pic.leetcode-cn.com/e9209625042bf37833e47a77ed9aea28021eb28cb57dcdd16eae2db6f75556ee-Picture5.png)

![img](https://pic.leetcode-cn.com/9460f5bd333440f1e41420d66ec81fc6c5b205aed7230a52882595efc95245bc-Picture6.png)

![img](https://pic.leetcode-cn.com/e139234ec8c49f23647e72b3b6cfa1d3907b451e33f9edfe09e8799efc370988-Picture7.png)

![img](https://pic.leetcode-cn.com/5e7bfde259de5fcf50c81222ad1330d9a50cdf41f8bc3fddda710db59574091e-Picture8.png)

![img](https://pic.leetcode-cn.com/e8f9f419e91b180c7c299897358d884a274a1500ba07489785c6f8382413c49f-Picture9.png)

<img src="https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427060725.png" alt="image-20210427060723846" style="zoom:200%;" />

###### 代码

```java
public boolean verifyPostorder(int[] postorder) {
    return helper(postorder, 0, postorder.length - 1);
}

boolean helper(int[] postorder, int left, int right) {
    //如果left==right，就一个节点不需要判断了，如果left>right说明没有节点，
    //也不用再看了,否则就要继续往下判断
    if (left >= right)
        return true;
    //因为数组中最后一个值postorder[right]是根节点，这里从左往右找出第一个比
    //根节点大的值，他后面的都是根节点的右子节点（包含当前值，不包含最后一个值，
    //因为最后一个是根节点），他前面的都是根节点的左子节点
    int mid = left;
    int root = postorder[right];
    while (postorder[mid] < root)
        mid++;
    int temp = mid;
    //因为postorder[mid]前面的值都是比根节点root小的，
    //我们还需要确定postorder[mid]后面的值都要比根节点root大，
    //如果后面有比根节点小的直接返回false
    while (temp < right) {
        if (postorder[temp++] < root)
            return false;
    }
    //然后对左右子节点进行递归调用
    return helper(postorder, left, mid - 1) && helper(postorder, mid, right - 1);
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/solution/di-gui-he-zhan-liang-chong-fang-shi-jie-jue-zui-ha/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210427060847435](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427060848.png)

![image-20210427061510049](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427061510.png)

#### （2）单调栈

![image-20210427083615750](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427083617.png)

![image-20210427090603158](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427090604.png)

###### 代码

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        // 单调栈使用，单调递增的单调栈
        Deque<Integer> stack = new LinkedList<>();
        // 表示上一个根节点的元素，这里可以把postorder的最后一个元素root看成无穷大节点的左孩子
        int pervElem = Integer.MAX_VALUE;
        // 逆向遍历，就是翻转的先序遍历
        for (int i = postorder.length - 1;i>=0;i--){
            // 左子树元素必须要小于递增栈被peek访问的元素，否则就不是二叉搜索树
            if (postorder[i] > pervElem){
                return false;
            }
            while (!stack.isEmpty() && postorder[i] < stack.peek()){
                // 数组元素小于单调栈的元素了，表示往左子树走了，记录下上个根节点
                // 找到这个左子树对应的根节点，之前右子树全部弹出，不再记录，因为不可能在往根节点的右子树走了
                pervElem = stack.pop();
            }
            // 这个新元素入栈
            stack.push(postorder[i]);
        }
        return true;
    }
}

作者：burning-summer
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/solution/dan-diao-di-zeng-zhan-by-shi-huo-de-xia-tian/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210427085955611](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427085956.png)

## 153、[二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

![image-20210427094532886](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427094534.png)

![image-20210427094545765](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427094546.png)

### 思路

#### （1）递归

###### 代码

```java
class Solution {
    Node pre, head;
    public Node treeToDoublyList(Node root) {
        if(root == null) return null;
        dfs(root);
        head.left = pre;
        pre.right = head;
        return head;
    }
    void dfs(Node cur) {
        if(cur == null) return;
        dfs(cur.left);
        if(pre != null) pre.right = cur;
        else head = cur;
        cur.left = pre;
        pre = cur;
        dfs(cur.right);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/solution/mian-shi-ti-36-er-cha-sou-suo-shu-yu-shuang-xian-5/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210427094902618](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427094903.png)



###### 复杂度分析

![image-20210427095624039](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427095625.png)

![image-20210427095640970](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427095641.png)



#### （2）中序遍历非递归写法

```java
    public Node treeToDoublyList(Node root) {
        //边界条件判断
        if (root == null)
            return root;
        Stack<Node> stack = new Stack<>();
        Node head = null;
        Node pre = null;
        while (root != null || !stack.isEmpty()) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            if (!stack.isEmpty()) {
                root = stack.pop();
                //下面是对当前节点的操作
                if (pre == null) {
                    //这行代码只会执行一次，就是root是树的左子节点的
                    //左子节点的……,一直没有左子节点为止,实际上就是中
                    //序遍历结果的第一个节点
                    head = root;
                } else {
                    //串起来的结果就是前一个节点pre的right指向当前节点，
                    //然后当前节点的left指向前一个节点pre
                    pre.right = root;
                }
                root.left = pre;
                //前一个节点和当前节点串起来之后，就让当前节点变成前一个节点
                pre = root;
                
                root = root.right;
            }
        }
        //首尾穿起来
        head.left = pre;
        pre.right = head;
        return head;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/solution/shu-ju-jie-gou-he-suan-fa-3chong-shi-xia-bucn/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210427212940677](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427212941.png)

![image-20210427213804503](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427213805.png)



## 154、[38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

![image-20210427213912446](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210427213913.png)

### 思路

#### （1）回溯法/固定位置去重

![image-20210428061405710](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428061407.png)

![image-20210428061734844](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428061735.png)

![image-20210428061859111](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428061859.png)

![img](https://pic.leetcode-cn.com/1599403497-wEILvT-Picture4.png)

![img](https://pic.leetcode-cn.com/1599403497-eBHuvg-Picture5.png)

![image-20210428062130678](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428062131.png)

![img](https://pic.leetcode-cn.com/1599403497-BvuqQX-Picture7.png)

![image-20210428062238252](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428062238.png)

![img](https://pic.leetcode-cn.com/1599403497-crBxOP-Picture9.png)

![img](https://pic.leetcode-cn.com/1599403497-lwELHl-Picture10.png)

![img](https://pic.leetcode-cn.com/1599403497-yjHsYa-Picture11.png)

![img](https://pic.leetcode-cn.com/1599403497-oYRpaR-Picture12.png)

![img](https://pic.leetcode-cn.com/1599403497-CadkyC-Picture13.png)

![img](https://pic.leetcode-cn.com/1599403497-Sbywbs-Picture14.png)

![img](https://pic.leetcode-cn.com/1599403497-RNgwPK-Picture15.png)

![img](https://pic.leetcode-cn.com/1599403497-nOToNd-Picture16.png)

![image-20210428062459986](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428062500.png)

![img](https://pic.leetcode-cn.com/1599403497-xvTQhj-Picture18.png)

###### 代码

![image-20210428063840667](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428063841.png)

```java
class Solution {
    List<String> res = new LinkedList<>();
    char[] c;
    public String[] permutation(String s) {
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);
    }
    void dfs(int x) {
        if(x == c.length - 1) {
            //直接交换char数组然后作为了结果加入到最后的list中
            res.add(String.valueOf(c));      // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();
        for(int i = x; i < c.length; i++) {
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x);                      // 交换，将 c[i] 固定在第 x 位
            dfs(x + 1);                      // 开启固定第 x + 1 位字符
            swap(i, x);                      // 恢复交换
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/solution/mian-shi-ti-38-zi-fu-chuan-de-pai-lie-hui-su-fa-by/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210428063919149](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428063919.png)

![image-20210428072959846](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428073000.png)

#### （2）回溯2

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {

    public String[] permutation(String s) {
        int len = s.length();
        if (len == 0) {
            return new String[0];
        }

        // 转换成字符数组是常见的做法
        char[] charArr = s.toCharArray();
        // 排序是为了去重方便
        Arrays.sort(charArr);

        // 由于操作的都是字符，使用 StringBuilder
        StringBuilder path = new StringBuilder();
        boolean[] used = new boolean[len];

        // 为了方便收集结果，使用动态数组
        List<String> res = new ArrayList<>();
        dfs(charArr, len, 0, used, path, res);

        // 记得转成字符串数组
        return res.toArray(new String[0]);
    }

    /**
     * @param charArr 字符数组
     * @param len     字符数组的长度
     * @param depth   当前递归深度
     * @param used    当前字符是否使用
     * @param path    从根结点到叶子结点的路径
     * @param res     保存结果集的变量
     */
    private void dfs(char[] charArr,
                     int len,
                     int depth,
                     boolean[] used,
                     StringBuilder path,
                     List<String> res) {
        if (depth == len) {
            // path.toString() 恰好生成了新的字符对象
            res.add(path.toString());
            return;
        }
        for (int i = 0; i < len; i++) {
            if (!used[i]) {
                if (i > 0 && charArr[i] == charArr[i - 1] && !used[i - 1]) {
                    continue;
                }
                used[i] = true;
                path.append(charArr[i]);

                dfs(charArr, len, depth + 1, used, path, res);

                // 递归完成以后，需要撤销选择，递归方法执行之前做了什么，递归方法执行以后就需要做相应的逆向操作
                path.deleteCharAt(path.length() - 1);
                used[i] = false;
            }
        }
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        String[] res = solution.permutation("aba");
        System.out.println(Arrays.toString(res));
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/solution/hui-su-suan-fa-java-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 155、[40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

![image-20210428085658463](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428085700.png)

### 思路

#### （1）快排

![image-20210428091228849](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428091229.png)

![image-20210428091350453](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428091351.png)

![image-20210428091640459](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428091641.png)

![img](https://pic.leetcode-cn.com/1612615167-smzuGu-Picture5.png)

![img](https://pic.leetcode-cn.com/1612615167-WXDRIU-Picture6.png)

![img](https://pic.leetcode-cn.com/1612615167-lWmBlA-Picture7.png)

![img](https://pic.leetcode-cn.com/1612615167-ghPekO-Picture8.png)

![img](https://pic.leetcode-cn.com/1612615167-azMHYj-Picture9.png)

![img](https://pic.leetcode-cn.com/1612615167-FGaWKZ-Picture10.png)

![image-20210428092012916](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428092013.png)

###### 代码

![image-20210511004918225](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511004920.png)

![image-20210511005000547](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511005001.png)

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        quickSort(arr, 0, arr.length - 1);
        return Arrays.copyOf(arr, k);
    }
    private void quickSort(int[] arr, int l, int r) {
        // 子数组长度为 1 时终止递归
        if (l >= r) return;
        // 哨兵划分操作（以 arr[l] 作为基准数）
        int i = l, j = r;
        while (i < j) {
            //注意这里必须从右开始找，要是从左开始，停下的时候i对应的值肯定大于基准值的
            //但是因为i<j,所以j被迫会停下，可能没有指到最小的基准值就停下了
            while (i < j && arr[j] >= arr[l]) j--;
            while (i < j && arr[i] <= arr[l]) i++;
            swap(arr, i, j);
        }
        //这里是交换基准位置和相遇位置，把数组按照基准数分成两个部分，交换后，基准位置的数都不变		//了,因为两边的数都比基准数字大或者小
        swap(arr, i, l);
        // 递归左（右）子数组执行哨兵划分
        quickSort(arr, l, i - 1);
        quickSort(arr, i + 1, r);
    }
    private void swap(int[] arr, int i, int j) {
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/jian-zhi-offer-40-zui-xiao-de-k-ge-shu-j-9yze/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210428092539448](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428092540.png)

![image-20210428094027818](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428094028.png)

#### （2）快排优化使用

![image-20210428095504318](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428095505.png)

![image-20210428100334789](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210428100336.png)

![img](https://pic.leetcode-cn.com/1612615167-XApQqA-Picture13.png)

![img](https://pic.leetcode-cn.com/1612615167-AjiQZD-Picture14.png)

![img](https://pic.leetcode-cn.com/1612615167-HWPotV-Picture15.png)

![img](https://pic.leetcode-cn.com/1612615167-TPvFMZ-Picture16.png)

![image-20210429061051013](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101200.png)

###### 代码

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k >= arr.length) return arr;
        return quickSort(arr, k, 0, arr.length - 1);
    }
    private int[] quickSort(int[] arr, int k, int l, int r) {
        int i = l, j = r;
        while (i < j) {
            while (i < j && arr[j] >= arr[l]) j--;
            while (i < j && arr[i] <= arr[l]) i++;
            swap(arr, i, j);
        }
        swap(arr, i, l);
        if (i > k) return quickSort(arr, k, l, i - 1);
        if (i < k) return quickSort(arr, k, i + 1, r);
        return Arrays.copyOf(arr, k);
    }
    private void swap(int[] arr, int i, int j) {
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/jian-zhi-offer-40-zui-xiao-de-k-ge-shu-j-9yze/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210429061700633](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429061701.png)

![image-20210429061835924](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429061836.png)

#### （3）快排写法3

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 最后一个参数表示我们要找的是下标为k-1的数
        return quickSearch(arr, 0, arr.length - 1, k - 1);
    }

    private int[] quickSearch(int[] nums, int lo, int hi, int k) {
        // 每快排切分1次，找到排序后下标为j的元素，如果j恰好等于k就返回j以及j左边所有的数；
        int j = partition(nums, lo, hi);
        if (j == k) {
            return Arrays.copyOf(nums, j + 1);
        }
        // 否则根据下标j与k的大小关系来决定继续切分左段还是右段。
        return j > k? quickSearch(nums, lo, j - 1, k): quickSearch(nums, j + 1, hi, k);
    }

    // 快排切分，返回下标j，使得比nums[j]小的数都在j的左边，比nums[j]大的数都在j的右边。
    private int partition(int[] nums, int lo, int hi) {
        int v = nums[lo];
        int i = lo, j = hi + 1;
        while (true) {
            while (++i <= hi && nums[i] < v);
            while (--j >= lo && nums[j] > v);
            if (i >= j) {
                break;
            }
            int t = nums[j];
            nums[j] = nums[i];
            nums[i] = t;
        }
        nums[lo] = nums[j];
        nums[j] = v;
        return j;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/3chong-jie-fa-miao-sha-topkkuai-pai-dui-er-cha-sou/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### （4）堆排序

![image-20210429064306628](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429064307.png)

![入堆出堆的过程](https://pic.leetcode-cn.com/ffd837f564946747ff6fca9df7568fa894494f2e46fe0fe2d6590123432f0a57.gif)

![image-20210429064414856](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429064416.png)

###### 代码

```java
public int[] getLeastNumbers(int[] arr, int k) {
    if (k == 0) {
        return new int[0];
    }
    // 使用一个最大堆（大顶堆）
    // Java 的 PriorityQueue 默认是小顶堆，添加 comparator 参数使其变成最大堆
    Queue<Integer> heap = new PriorityQueue<>(k, (i1, i2) -> Integer.compare(i2, i1));

    for (int e : arr) {
        // 当前数字小于堆顶元素才会入堆
        if (heap.isEmpty() || heap.size() < k || e < heap.peek()) {
            heap.offer(e);
        }
        //上面由于大小等于k后如果有更小的元素，还是会入队，所以这边要删掉大的元素
        if (heap.size() > k) {
            heap.poll(); // 删除堆顶最大元素
        }
    }

    // 将堆中的元素存入数组
    int[] res = new int[heap.size()];
    int j = 0;
    for (int e : heap) {
        res[j++] = e;
    }
    return res;
}

作者：nettee
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/tu-jie-top-k-wen-ti-de-liang-chong-jie-fa-you-lie-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
    
```

###### 复杂度分析

![image-20210429064551743](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429064552.png)

![image-20210429070948350](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429070949.png)

#### （5）二叉搜索树



![image-20210429071754152](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429071754.png)

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // TreeMap的key是数字, value是该数字的个数。
        // cnt表示当前map总共存了多少个数字。
        TreeMap<Integer, Integer> map = new TreeMap<>();
        int cnt = 0;
        for (int num: arr) {
            // 1. 遍历数组，若当前map中的数字个数小于k，则map中当前数字对应个数+1
            if (cnt < k) {
                map.put(num, map.getOrDefault(num, 0) + 1);
                cnt++;
                continue;
            } 
            // 2. 否则，取出map中最大的Key（即最大的数字), 判断当前数字与map中最大数字的大小关系：
            //    若当前数字比map中最大的数字还大，就直接忽略；
            //    若当前数字比map中最大的数字小，则将当前数字加入map中，并将map中的最大数字的个数-1。
            Map.Entry<Integer, Integer> entry = map.lastEntry();
            if (entry.getKey() > num) {
                map.put(num, map.getOrDefault(num, 0) + 1);
                if (entry.getValue() == 1) {
                    map.pollLastEntry();
                } else {
                    map.put(entry.getKey(), entry.getValue() - 1);
                }
            }
            
        }

        // 最后返回map中的元素
        int[] res = new int[k];
        int idx = 0;
        for (Map.Entry<Integer, Integer> entry: map.entrySet()) {
            int freq = entry.getValue();
            while (freq-- > 0) {
                res[idx++] = entry.getKey();
            }
        }
        return res;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/3chong-jie-fa-miao-sha-topkkuai-pai-dui-er-cha-sou/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



#### （6）数据有限时直接计数排序 O(N)

![image-20210429072432877](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429072433.png)

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 统计每个数字出现的次数
        int[] counter = new int[10001];
        for (int num: arr) {
            counter[num]++;
        }
        // 根据counter数组从头找出k个数作为返回结果
        int[] res = new int[k];
        int idx = 0;
        for (int num = 0; num < counter.length; num++) {
            //这里必须要是while，不能是if，因为有重复元素，所以要取到该位置的元素为0为止
            //这样才是当前的元素都取完了
            while (counter[num]-- > 0 && idx < k) {
                res[idx++] = num;
            }
            if (idx == k) {
                break;
            }
        }
        return res;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/3chong-jie-fa-miao-sha-topkkuai-pai-dui-er-cha-sou/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 156、[数字序列中某一位的数字](https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

![image-20210429092452638](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429092454.png)

### 思路

#### （1）找规律

![image-20210429093105456](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429093106.png)

![image-20210429093416210](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429093417.png)

###### 代码

```java
class Solution {
    public int findNthDigit(int n) {
       if(n<0) return 0;
	   else if(n>=0 && n<=9) return n; 
	   else {
		   long m = n;
		   long temp = 0;
		   long base = 1;
		   long num = 9;
		   char res = '0';
		   while((temp+base*num) < m) {
			   temp += base*num;
			   base += 1;
			   num *= 10;
		   }
		   long a = (m-temp)/base;
		   long b = (m-temp)%base;
		   if(b!=0) {
			   long c = (long) (Math.pow(10, base-1) + a);
			   res = String.valueOf(c).charAt((int)b-1);
		   }else {
			   long c = (long) (Math.pow(10, base-1) + a - 1);
			   res = String.valueOf(c).charAt((int)base-1);
		   }
		   return Integer.parseInt(String.valueOf(res));
	   }

    }
}

作者：zhuguangyu
链接：https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/solution/shuang-100chao-hao-li-jie-xiang-xi-ju-li-tong-su-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 157、[45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

![image-20210429194855926](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429194859.png)

### 思路

#### （1）快排比较

![image-20210429203853823](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429203854.png)

###### 代码

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for(int i = 0; i < nums.length; i++)
            strs[i] = String.valueOf(nums[i]);
        quickSort(strs, 0, strs.length - 1);
        StringBuilder res = new StringBuilder();
        for(String s : strs)
            res.append(s);
        return res.toString();
    }
    void quickSort(String[] strs, int l, int r) {
        if(l >= r) return;
        int i = l, j = r;
        //这里是要复用变量，可以直接给null
        String tmp = strs[i];
        while(i < j) {
            while((strs[j] + strs[l]).compareTo(strs[l] + strs[j]) >= 0 && i < j) j--;
            while((strs[i] + strs[l]).compareTo(strs[l] + strs[i]) <= 0 && i < j) i++;
            //跳出循环的时候，i==j，重合了，所以下面是原地交换
            tmp = strs[i];
            strs[i] = strs[j];
            strs[j] = tmp;
        }
        //这里就再交换下i和l的位置的值，tmp就是存的strs[i]的值
        strs[i] = strs[l];
        strs[l] = tmp;
        quickSort(strs, l, i - 1);
        quickSort(strs, i + 1, r);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/solution/mian-shi-ti-45-ba-shu-zu-pai-cheng-zui-xiao-de-s-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210429203931688](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429203931.png)

![image-20210429230205890](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429230206.png)



#### （2）内置排序函数

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] strs = new String[nums.length];
        for(int i = 0; i < nums.length; i++)
            strs[i] = String.valueOf(nums[i]);
        //如果x>y，则比较结果为正，Arrays.sort()就是如果为正就要交换两个值
        Arrays.sort(strs, (x, y) -> (x + y).compareTo(y + x));
        StringBuilder res = new StringBuilder();
        for(String s : strs)
            res.append(s);
        return res.toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/solution/mian-shi-ti-45-ba-shu-zu-pai-cheng-zui-xiao-de-s-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210429230135104](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210429230136.png)

## 158、[ 46. 把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

![image-20210430082224699](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101201.png)

### 思路

#### （1）动态规划

![image-20210430092730873](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430092734.png)

![image-20210430092753782](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430092755.png)

##### 字符串遍历

![image-20210430092931635](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430092932.png)



![img](https://pic.leetcode-cn.com/f61ef2e376f0630ac9cf7cbd50701e64f73c9eb780bd942bd0697b92e3e13483-Picture2.png)

![image-20210430093109864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430093110.png)



![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430093110.png)

![img](https://pic.leetcode-cn.com/f1f66ffc93d25976f0b599135c7bf2efcf496cb05f370b87fad55660e16ff7c1-Picture5.png)

![img](https://pic.leetcode-cn.com/14319cb014105648e9f360c7916c7d4985648992b337c3308b5c6088f3da4dde-Picture6.png)

![img](https://pic.leetcode-cn.com/4fc0ea440e9c493e9a82fb8313489edfd0b9612c8f33ac6178ca68d241647149-Picture7.png)

![img](https://pic.leetcode-cn.com/ebfd2d2b5a94ec34aaf7bdbbb72db5b8398ed6eb17c9842564a0d32179d90f5e-Picture8.png)

![img](https://pic.leetcode-cn.com/c13a47704f2f533c98a6c4175cacb858d54fd193fb9fec0f39e5f7b8d7735e4c-Picture9.png)

![img](https://pic.leetcode-cn.com/46d1fbb9a5943ddc56a48fa52d90447f4cf19467d67e6a44ae9b04dbc9804841-Picture10.png)

![img](https://pic.leetcode-cn.com/931cfb6eebc6b28ce255a4519a7cfa4ceee112ead044f4fc61f1eda46f13b9d7-Picture11.png)





###### 代码

```java
class Solution {
    public int translateNum(int num) {
        String s = String.valueOf(num);
        int a = 1, b = 1;
        //i从2开始，因为0和1位数字方案数都是1
        for(int i = 2; i <= s.length(); i++) {
            String tmp = s.substring(i - 2, i);
            int c = tmp.compareTo("10") >= 0 && tmp.compareTo("25") <= 0 ? a + b : a;
            b = a;
            a = c;
        }
        return a;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/solution/mian-shi-ti-46-ba-shu-zi-fan-yi-cheng-zi-fu-chua-6/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210430093826129](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430093826.png)

![image-20210430093854202](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430093855.png)

#### （2）动态规划空间优化

![image-20210430094851116](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430094852.png)

###### ![img](https://pic.leetcode-cn.com/da5ae549c4234a42615bb6ee3a10422343a71c1b2aa33030eeaa71ca2279ba33-Picture12.png)

![img](https://pic.leetcode-cn.com/c2ac9126580a30366f88e1b27c3052cc4fee7afcebb67e07b6aeaf8c0d4d59ae-Picture13.png)

![img](https://pic.leetcode-cn.com/b37c9b4354496605716bdc50875e2138394a901fdb33885dae9de08a96570967-Picture14.png)

![img](https://pic.leetcode-cn.com/73a0e471ae489420586ff77a5267f71617eb3fc2dd18c7443ebbf1de248252db-Picture15.png)

![img](https://pic.leetcode-cn.com/9621f79fa2954c0ed34f0b278f66f54979af80389fb59c58305efd9848dda783-Picture16.png)



![img](https://pic.leetcode-cn.com/40e0fc171c3a6950bf45c904987a2df52002683c5e35b48461cb2d71c7e7ed8b-Picture17.png)



![img](https://pic.leetcode-cn.com/be7380e708c6877b4aa78364a0e3eb1e20fb3f540b8bf0bf52a5e63654c51117-Picture18.png)

![img](https://pic.leetcode-cn.com/a955c2105d4205b95a0d82453b3ea16cb7d111930fa1d85813a20ff9e80f0317-Picture19.png)

![img](https://pic.leetcode-cn.com/46211327a9433d5c462ba93b98060b30e34be677d8ced78a6eb354eb265d42c2-Picture20.png)

![img](https://pic.leetcode-cn.com/5788b3fc0278b6439f6ff38f1e743af08f471635920c3125706bca16a9287538-Picture21.png)



![img](https://pic.leetcode-cn.com/dd93aaf57d1189bfe33279b5dbf00e34a07ee66e9eb7afdce130170133b0657c-Picture22.png)

![img](https://pic.leetcode-cn.com/1967146d2f685356c5e6bba36e1da5226879c928c9b470f9cdbfef75f6a657ff-Picture23.png)

![img](https://pic.leetcode-cn.com/dba68cc3968b956fff2812586a12d8efe1cc728084f50abd74e215a0d6c730ec-Picture24.png)

![img](https://pic.leetcode-cn.com/3cbf3b45f977cc15bc1435c13d36556a8e11e44e8e96bc60f43578fbe511cac9-Picture25.png)



###### 代码

```java
class Solution {
    public int translateNum(int num) {
        int a = 1, b = 1, x, y = num % 10;
        while(num != 0) {
            num /= 10;
            x = num % 10;
            int tmp = 10 * x + y;
            int c = (tmp >= 10 && tmp <= 25) ? a + b : a;
            b = a;
            a = c;
            y = x;
        }
        return a;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/solution/mian-shi-ti-46-ba-shu-zi-fan-yi-cheng-zi-fu-chua-6/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210430095309396](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430095310.png)

![image-20210430095334259](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430095335.png)

#### （3）递归

![幻灯片1.JPG](https://pic.leetcode-cn.com/a12a168d3c88812a1d49277324898b289bf35beb059da1a59397f66c4ad1a269-%E5%B9%BB%E7%81%AF%E7%89%871.JPG)

![image-20210430102019828](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430102021.png)

###### 代码

```java
class Solution {
    public int translateNum(int num) {
        if (num<=9) {return 1;}
        //xyzcba
        int ba = num%100;
        if (ba<=9||ba>=26) {return translateNum(num/10);}
        else  {return translateNum(num/10)+translateNum(num/100);}
    }
}

作者：xiang-shang-de-gua-niu
链接：https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/solution/di-gui-qiu-jie-shuang-bai-by-xiang-shang-de-gua-ni/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210430102110205](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430102110.png)



## 159、[47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

![image-20210430102348876](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430102349.png)

### 思路

#### （1）动态规划

![image-20210430103412320](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430103413.png)

![image-20210430103701314](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430103702.png)

![img](https://pic.leetcode-cn.com/67cf85128a890bac4a7e38062f728ce536ebecd6c3595dd99e94f7f4cb2edd9f-Picture2.png)

![img](https://pic.leetcode-cn.com/f74a7d8a2b3d6151c3ffad67091ccb6e7360301d7d2e2b4d57c1f8cbdd4d439c-Picture3.png)

![img](https://pic.leetcode-cn.com/7885d2fb7a18b497d242f09076df8b2e7f16c92d949c0d5c04416987ab262a0c-Picture4.png)

![img](https://pic.leetcode-cn.com/df35c99048191872eabf260600a7f8ca33e11b71374a1245c9924a5012788e7a-Picture5.png)

![img](https://pic.leetcode-cn.com/68914728aa4e45427315b07f2711970c2917dbac03d78721a4e898ac360b56ca-Picture6.png)

![img](https://pic.leetcode-cn.com/9cb66e7ed62cdd0d4ee32f54626082e238d1791bf0edea7b34d416c645309350-Picture7.png)

![img](https://pic.leetcode-cn.com/60abcaadc19c3c9616377d148f337ae3b9ec6235c4628177a646de2daf379b1a-Picture8.png)

![img](https://pic.leetcode-cn.com/29dae0242239cbae12878a8587917b63c090c65839f356d2b087aabe26a3f1fe-Picture9.png)

![img](https://pic.leetcode-cn.com/7d62208403a02b8439fecec0e37b3910e47dfe3a0280af7d9f438e0a992005f3-Picture10.png)

![img](https://pic.leetcode-cn.com/d7005f30bf7bfc9e41860fa186e8f03c2481256323f3d66bfbf0852f08f5e57b-Picture11.png)

###### 代码

```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(i == 0 && j == 0) continue;
                if(i == 0) grid[i][j] += grid[i][j - 1] ;
                else if(j == 0) grid[i][j] += grid[i - 1][j];
                else grid[i][j] += Math.max(grid[i][j - 1], grid[i - 1][j]);
            }
        }
        return grid[m - 1][n - 1];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/solution/mian-shi-ti-47-li-wu-de-zui-da-jie-zhi-dong-tai-gu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210430104803861](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430104804.png)

#### （2）动态规划优化

![image-20210430105325744](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430105326.png)

```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        for(int j = 1; j < n; j++) // 初始化第一行
            grid[0][j] += grid[0][j - 1];
        for(int i = 1; i < m; i++) // 初始化第一列
            grid[i][0] += grid[i - 1][0];
        for(int i = 1; i < m; i++)
            for(int j = 1; j < n; j++) 
                grid[i][j] += Math.max(grid[i][j - 1], grid[i - 1][j]);
        return grid[m - 1][n - 1];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/solution/mian-shi-ti-47-li-wu-de-zui-da-jie-zhi-dong-tai-gu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210430105350952](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430105351.png)

## 160、[49. 丑数](https://leetcode-cn.com/problems/chou-shu-lcof/)

![image-20210430105543347](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430105544.png)

### 思路

#### （1）动态规划

![image-20210430121022062](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430121023.png)

![image-20210430121320984](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430121322.png)

```json
我的一点理解： 在已有的丑数序列上每一个数都必须乘2， 乘3， 乘5， 这样才不会漏掉某些丑数。假设已有的丑数序列为[1, 2, 3, ..., n1, n2], 如果单纯的让每个丑数乘2， 乘3， 乘5顺序排列的话肯定会有问题，

比如如果按照这样的顺序排列下去肯定有问题[1*2, 1*3, 1*5, 2*2, 2*3, 2*5, 3*2, 3*3, 3*5, ... , n1 *2, n1 * 3, n1 * 5, n2 * 2, n3* 3, n2 * 5]，因为后面乘2的数据可能会比前面乘3乘5的数据要小，那这个乘2的数应该排在他们的前面， 后面乘3的数据也可能比前面乘5的数据要小，那这个乘3的数应该排在他们的前面。

那怎么办呢，每个数都必须乘2， 乘3， 乘5这样才能保证求出所有的丑数，而且还要保证丑数的顺序，这个改如何同时实现呢？

通过观察网上的各个题解，终于找到了办法，那就是记录每个丑数是否已经被乘2， 乘3， 乘5了， 具体的做法是

设置3个索引a, b, c，分别记录前几个数已经被乘2， 乘3， 乘5了，比如a表示前(a-1)个数都已经乘过一次2了，下次应该乘2的是第a个数；b表示前(b-1)个数都已经乘过一次3了，下次应该乘3的是第b个数；c表示前(c-1)个数都已经乘过一次5了，下次应该乘5的是第c个数；

对于某个状态下的丑数序列，我们知道此时第a个数还没有乘2(有没有乘3或者乘5不知道）， 第b个数还没有乘3(有没有乘2或者乘5不知道），第c个数还没有乘5(有没有乘2或者乘3不知道), 下一个丑数一定是从第a丑数乘2， 第b个数乘3， 第c个数乘5中获得，他们三者最小的那个就是下个丑数。

求得下个丑数后就得判断这个丑数是谁，是某个数通过乘2得到的，还是某个数乘3得到的，又或是说某个数通过乘5得到的。我们可以比较一下这个新的丑数等于究竟是等于第a个丑数乘2, 还是第b个数乘3， 还是第c个数乘5， 通过比较我们肯定可以知道这个新的丑数到底是哪个数通过乘哪个数得到的。假设这个新的丑数是通过第a个数乘2得到的，说明此时第a个数已经通过乘2得到了一个新的丑数，那下个通过乘2得到一个新的丑数的数应该是第(a+1)个数，此时我们可以说前 a 个数都已经乘过一次2了，下次应该乘2的是第 （a+1） 个数, 所以a++；如果新的丑数是通过第b个数乘3得到的, 说明此时第 b个数已经通过乘3得到了一个新的丑数，那下个需要通过乘3得到一个新的丑数的数应该是第(b+1)个数，此时我们可以说前 b 个数都已经乘过一次3了，下次应该乘3的是第 （b+1） 个数, 所以 b++；同理，如果这个这个新的丑数是通过第c个数乘5得到的, 那么c++;

但是注意，如果第a个数乘2后等于第b个数乘3，或者等于第c个数乘5， 说明这个新的丑数是有两种或者三种方式可以得到，这时应该给得到这个新丑数的组合对应的索引都加一，比如新丑数是第a个数乘2后和第b个数乘3得到的，那么 a 和 b都应该加一， 因为此时第a个数已经通过乘2得到了一个新的丑数，第b个数已经通过乘3得到了一个新的丑数, 只不过这两个数相等而已。所以我们给计数器加一的时候不能使用 if else else if， 而应该使用if, if, if, 这样才不会把应该加一的计数器漏掉

经过n次循环，就能得到第n 个丑数了。
```

![image-20210430123148817](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430123149.png)

![image-20210430121640737](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430121641.png)

![img](https://pic.leetcode-cn.com/1603023853-bcETWT-Picture2.png)

![image-20210430122521797](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430122522.png)



![img](https://pic.leetcode-cn.com/1603023853-HrvzLL-Picture4.png)

![img](https://pic.leetcode-cn.com/1603023853-CzbMnV-Picture5.png)

![img](https://pic.leetcode-cn.com/1603023853-MoDFOM-Picture6.png)

![image-20210430122804804](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430122805.png)

![img](https://pic.leetcode-cn.com/1603023853-NYylSy-Picture8.png)



![img](https://pic.leetcode-cn.com/1603023853-saAyFf-Picture9.png)

![img](https://pic.leetcode-cn.com/1603023853-IALBSE-Picture10.png)

![img](https://pic.leetcode-cn.com/1603023853-kMoqKL-Picture11.png)

![image-20210430122950727](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430122951.png)



![img](https://pic.leetcode-cn.com/1603023853-tsYuXg-Picture12.png)



###### 代码

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] dp = new int[n];  // 使用dp数组来存储丑数序列
        dp[0] = 1;  // dp[0]已知为1
        int a = 0, b = 0, c = 0;    // 下个应该通过乘2来获得新丑数的数据是第a个， 同理b, c

        for(int i = 1; i < n; i++){
            // 第a丑数个数需要通过乘2来得到下个丑数，第b丑数个数需要通过乘2来得到下个丑数，同理第c个数
            int n2 = dp[a] * 2, n3 = dp[b] * 3, n5 = dp[c] * 5;
            dp[i] = Math.min(Math.min(n2, n3), n5);
            if(dp[i] == n2){
                a++; // 第a个数已经通过乘2得到了一个新的丑数，那下个需要通过乘2得到一个新的丑数的数应该是第(a+1)个数
            }
            if(dp[i] == n3){
                b++; // 第 b个数已经通过乘3得到了一个新的丑数，那下个需要通过乘3得到一个新的丑数的数应该是第(b+1)个数
            }
            if(dp[i] == n5){
                c++; // 第 c个数已经通过乘5得到了一个新的丑数，那下个需要通过乘5得到一个新的丑数的数应该是第(c+1)个数
            }
        }
        return dp[n-1];
    }
}
```



###### 复杂度分析

![image-20210430122209346](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430122210.png)

## 161、[54. 二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

![image-20210430140053705](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430140055.png)

### 思路

#### （1）中序遍历

![image-20210430141503901](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430141504.png)

![image-20210430141552346](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430141553.png)



![img](https://pic.leetcode-cn.com/e34ef69025595a78434979a4be06a58b2add26ec11c56c764e73f37026e23282-Picture1.png)

![image-20210430141630360](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430141631.png)

![img](https://pic.leetcode-cn.com/1b64db28be8a3e70bce2838a82a182fce8913add1edbc44affca492a2dbda597-Picture3.png)

![img](https://pic.leetcode-cn.com/e1726df25ed3542175bc113d1324a80da314f75f876c2189c7d6b3a0562cb9cc-Picture4.png)

![img](https://pic.leetcode-cn.com/0cc14ed09ec40612bb852b20580442b3d7794adfa8772203834ec678c31c12c5-Picture5.png)

![img](https://pic.leetcode-cn.com/0d73c98b73e942c5609e64b73d05571e113cf546fd219a14374367c460db2574-Picture6.png)



###### 复杂度分析

![image-20210430141413782](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430141414.png)

![image-20210430141719047](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430141719.png)

###### 代码

```java
class Solution {
    int res, k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k;
        dfs(root);
        return res;
    }
    void dfs(TreeNode root) {
        if(root == null) return;
        dfs(root.right);
        if(k == 0) return;
        //每次递归就把k-1，确定当前的结点的序号，当序号=0的结点就是目标结点了
        if(--k == 0) res = root.val;
        dfs(root.left);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/solution/mian-shi-ti-54-er-cha-sou-suo-shu-de-di-k-da-jie-d/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    int res, k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k;
        dfs(root);
        return res;
    }
    void dfs(TreeNode root) {
        if(root == null) return;
        dfs(root.right);
        //先--，再判断
        if(--k <= 0) 
            {
                res = root.val;
                return;//这里的return可以避免之后的无效迭代dfs(root.left);
            }
        dfs(root.left);
    }
}
```

#### （2）迭代

![image-20210430143111672](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430143112.png)

![image-20210430143129848](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430143131.png)

```java
class Solution {
    public int kthLargest(TreeNode root, int k) {
        int count = 1;
        Stack<TreeNode> stack = new Stack<>();
        while (Objects.nonNull(root) || !stack.empty()) {
            while (Objects.nonNull(root)) {
                stack.push(root);
                root = root.right;
            }
            TreeNode pop = stack.pop();
            if (count == k) {
                return pop.val;
            }
            count++;
            root = pop.left;
        }
        return 0;
    }
}

作者：yang_hang
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/solution/javaji-bai-100de-xiang-xi-jie-da-di-gui-he-die-dai/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 162、[56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

![image-20210430143929457](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430143930.png)

### 思路

#### （1）位运算

> 异或满足交换律，第一步异或，相同的数其实都抵消了，剩下两个不同的数。这两个数异或结果肯定有**某一位为1**，不然都是0的话就是相同数。找到这个位，**不同的两个数一个在此位为0，另一个为1**。**按此位将所有数分成两组**，回归异或的本质，1^0 = 1, 1^1 = 0, 0^0 = 1。a^b的结果里，**为1的位表明a与b在这一位上不相同**。这个不相同很关键，不相同就意味着我们在结果里**任选**一位为1的位置i，所有数可以**按照i位的取值（0,1）分成两组**，那么**a与b必然不在同一组**里。再对两组分别累计异或。那么两个异或结果就是a、b。分开后各自异或，相同的两个数异或肯定为0（而且分开的时候，两个数必为一组）。剩下的每组里就是我门要找的数。
>
> 难点主要在于对mask的理解。mask是一个二进制数，且其中只有一位是1，其他位全是0，比如000010，表示我们用倒数第二位作为分组标准，倒数第二位是0的数字分到一组，倒数第二位是1的分到另一组。
>
> 那么如何得到这个mask?因为我们分组的目的就**是将两个不重复数字分开**，这两个不重复数字的二进制表示肯定是不同的，但是我们没必要一位一位地比较，我们可以从右到左，找到**第一个不相同的位，将mask当中这一位变成1**,就得到了mask。
>
> 比如[2,2,3,3,4,6]中，**不重复的两个数字是4，6**，4和6的异或结果（也是整个数组的异或结果）是010，表示从右到左，第一次出现不同是在倒数第二位，（异或结果是1说明这个位置的数不同）那么可以确定，mask的倒数第二位是1，其他位是0，即010。这样4和mask的异或结果肯定跟6与mask的异或结果不同的，根据这个不同，将两个不同的数字分到两组去，而相同的数字跟mask异或的结果都是相同，肯定会到同一组去的

###### 代码

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        //用于将所有的数异或起来
        int k = 0;
        
        for(int num: nums) {
            k ^= num;
        }
        
        //获得k中最低位的1
        int mask = 1;

        //mask = k & (-k) 这种方法也可以得到mask，具体原因百度 哈哈哈哈哈
        //找到两个不同数字异或结果的为1的位置，表示两个数字在这位数字不同，一个为0，一个为1
        while((k & mask) == 0) {
            mask <<= 1;
        }
        
        int a = 0;
        int b = 0;
        
        for(int num: nums) {
            //相同的数与mask的结果，肯定一样，分到一组，不同的数，与mask的结果肯定不同，不在一			 //组
            if((num & mask) == 0) {
                a ^= num;
            } else {
                b ^= num;
            }
        }
        
        return new int[]{a, b};
    }
}

作者：eddieVim
链接：https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/solution/jie-di-qi-jiang-jie-fen-zu-wei-yun-suan-by-eddievi/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int x = 0, y = 0, n = 0, m = 1;
        for(int num : nums)               // 1. 遍历异或
            n ^= num;
        while((n & m) == 0)               // 2. 循环左移，计算 m
            m <<= 1;
        for(int num: nums) {              // 3. 遍历 nums 分组
            if((num & m) != 0) x ^= num;  // 4. 当 num & m != 0
            else y ^= num;                // 4. 当 num & m == 0
        }
        return new int[] {x, y};          // 5. 返回出现一次的数字
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/solution/jian-zhi-offer-56-i-shu-zu-zhong-shu-zi-tykom/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210430152620473](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430152621.png)

![image-20210430152740833](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430152742.png)

## 163、[56 - II. 数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

![image-20210430154156111](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430154157.png)

### 思路

#### （1）位运算

![Picture1.png](https://pic.leetcode-cn.com/28f2379be5beccb877c8f1586d8673a256594e0fc45422b03773b8d4c8418825-Picture1.png)

![image-20210430172959516](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430173000.png)

![image-20210430173018207](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430173018.png)



###### 代码

![image-20210430174104692](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430174105.png)

```java
public class Solution56_2 {
	
    public int singleNumber(int[] nums) {//本算法同样适用于数组nums中存在负数的情况
        if(nums.length==0) return -1;//输入数组长度不符合要求，返回-1;
        int[] bitSum = new int[32];//java int类型有32位，其中首位为符号位
        int res=0;
        for(int num:nums){
            int bitMask=1;//需要在这里初始化，不能和res一起初始化
            for(int i=31;i>=0;i--){//bitSum[0]为符号位
            	//这里同样可以通过num的无符号右移>>>来实现，否则带符号右移(>>)左侧会补符号位，对于负数会出错。
            	//但是不推荐这样做，最好不要修改原数组nums的数据
                //这里不能写成==1，因为随着mask左移，结果是2的n次方
                if((num&bitMask)!=0) bitSum[i]++;//这里判断条件也可以写为(num&bitMask)==bitMask,而不是==1
                bitMask=bitMask<<1;//左移没有无符号、带符号的区别，都是在右侧补0
            }
        }
        for(int i=0;i<32;i++){//这种做法使得本算法同样适用于负数的情况
            res=res<<1;
           //因为只有下次循环的时候，才要左移，当前位移到高位后再加新的位
            res+=bitSum[i]%3;//这两步顺序不能变，否则最后一步会多左移一次
        }
        return res;
    }
}


作者：mo-fei-25
链接：https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/solution/javashi-xian-jian-zhi-si-lu-wei-yun-suan-zhu-wei-t/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）状态机

看不懂。。。。



## 164、[57. 和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

![image-20210430222423109](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430222424.png)

### 思路

#### （1）对撞双指针

![image-20210430222520832](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430222522.png)

###### 代码

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int i = 0, j = nums.length - 1;
        while(i < j) {
            int s = nums[i] + nums[j];
            if(s < target) i++;
            else if(s > target) j--;
            else return new int[] { nums[i], nums[j] };
        }
        return new int[0];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/solution/mian-shi-ti-57-he-wei-s-de-liang-ge-shu-zi-shuang-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210430222905179](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430222905.png)

![image-20210430222919957](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430222920.png)

#### （2）HashMap

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = new int[2];
        // 数组不够两个数直接返回。
        if (nums == null || nums.length < 2) {
            return ans;
        }
        // 定义 HashMap 辅助搜索是否出现过符合条件的数。
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            // 循环遍历数组中的数，如果 map 中有符合条件的数则组队返回，否则加入 map 。
            int curr = target - nums[i];
            if (map.containsKey(curr)) {
                return new int[]{curr, nums[i]};
            }
            map.put(nums[i], i);
        }
        // 遍历完数组，没有符合条件的一对数。
        return ans;
    }
}

作者：iceblood
链接：https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/solution/1624ti-he-wei-sde-liang-ge-shu-zi-by-iceblood/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210430223341313](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430223342.png)



## 165、[57 - II. 和为s的连续正数序列](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)

![image-20210430223735823](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430223736.png)

### 思路

#### （1）滑动窗口

![image-20210430224736010](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430224737.png)

![image-20210430224830897](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430224831.png)

![image-20210430224845202](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430224846.png)

###### 代码

```java
public int[][] findContinuousSequence(int target) {
    int i = 1; // 滑动窗口的左边界
    int j = 1; // 滑动窗口的右边界
    int sum = 0; // 滑动窗口中数字的和
    List<int[]> res = new ArrayList<>();

    while (i <= target / 2) {
        if (sum < target) {
            // 右边界向右移动
            sum += j;
            j++;
        } else if (sum > target) {
            // 左边界向右移动
            sum -= i;
            i++;
        } else {
            // 记录结果
            int[] arr = new int[j-i];
            for (int k = i; k < j; k++) {
                arr[k-i] = k;
            }
            res.add(arr);
            // 左边界向右移动
            sum -= i;
            i++;
        }
    }

    return res.toArray(new int[res.size()][]);
}

作者：nettee
链接：https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/shi-yao-shi-hua-dong-chuang-kou-yi-ji-ru-he-yong-h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210430225109782](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430225110.png)



#### （2）滑动窗口2

![image-20210430230030366](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430230031.png)

![image-20210430230116462](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430230117.png)

###### 代码

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        //这样的话，就包含了左右边界了
        int i = 1, j = 2, s = 3;
        List<int[]> res = new ArrayList<>();
        while(i < j) {
            if(s == target) {
                int[] ans = new int[j - i + 1];
                for(int k = i; k <= j; k++)
                    //这个k-i表示离左边界移动的offset，从0开始
                    ans[k - i] = k;
                res.add(ans);
            }
            if(s >= target) {
                s -= i;
                i++;
            } else {
                j++;
                s += j;
            }
        }
        return res.toArray(new int[0][]);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/jian-zhi-offer-57-ii-he-wei-s-de-lian-xu-t85z/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210430230220157](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430230221.png)

#### （3）求和公式

![image-20210501094841677](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501094844.png)

![image-20210430232805128](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430232806.png)

![image-20210430232818870](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430232819.png)

###### 代码

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        int i = 1;
        double j = 2.0;
        List<int[]> res = new ArrayList<>();
        while(i < j) {
            j = (-1 + Math.sqrt(1 + 4 * (2 * target + (long) i * i - i))) / 2;
            if(i < j && j == (int)j) {
                int[] ans = new int[(int)j - i + 1];
                for(int k = i; k <= (int)j; k++)
                    ans[k - i] = k;
                res.add(ans);
            }
            i++;
        }
        return res.toArray(new int[0][]);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/jian-zhi-offer-57-ii-he-wei-s-de-lian-xu-t85z/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210430232922142](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210430232923.png)

![image-20210501100400851](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501100401.png)

## 166、[58 - II. 左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

![image-20210501100848357](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501100849.png)

### 思路

#### （1）字符串切片

![image-20210501101138436](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101139.png)

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        return s.substring(n, s.length()) + s.substring(0, n);
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/solution/mian-shi-ti-58-ii-zuo-xuan-zhuan-zi-fu-chuan-qie-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210501101238533](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101239.png)

![image-20210501101303769](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101304.png)



#### （2）列表遍历拼接

![image-20210501101621765](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101623.png)

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder res = new StringBuilder();
        for(int i = n; i < s.length(); i++)
            res.append(s.charAt(i));
        for(int i = 0; i < n; i++)
            res.append(s.charAt(i));
        return res.toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/solution/mian-shi-ti-58-ii-zuo-xuan-zhuan-zi-fu-chuan-qie-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 取余运算简化

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder res = new StringBuilder();
        for(int i = n; i < n + s.length(); i++)
            //小于length的时候，都是正常取，从n为开始，当超过s.length()-1的时候，取余的结果导				//致从开头0索引开始取
            res.append(s.charAt(i % s.length()));
        return res.toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/solution/mian-shi-ti-58-ii-zuo-xuan-zhuan-zi-fu-chuan-qie-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210501102259080](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501102259.png)

###### 复杂度分析

![image-20210501101657876](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101658.png)

![image-20210501101558923](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501101559.png)

#### （3）字符串遍历拼接

若规定 Python 不能使用 join() 函数，或规定 Java 只能用 String ，则使用此方法。

此方法与 方法二 思路一致，区别是使用字符串代替列表。

![Picture3.png](https://pic.leetcode-cn.com/7eda5b3b46a56ac72071ea4300fcc7af87b99c487a65d0b65e2c5caa2e117189-Picture3.png)

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        String res = "";
        for(int i = n; i < s.length(); i++)
            res += s.charAt(i);
        for(int i = 0; i < n; i++)
            res += s.charAt(i);
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/solution/mian-shi-ti-58-ii-zuo-xuan-zhuan-zi-fu-chuan-qie-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

同理，利用求余运算，可以简化代码。

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        String res = "";
        for(int i = n; i < n + s.length(); i++)
            res += s.charAt(i % s.length());
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/solution/mian-shi-ti-58-ii-zuo-xuan-zhuan-zi-fu-chuan-qie-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 167、[ 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

![image-20210501114934681](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501114936.png)

### 思路

#### （1）单调双向队列

![image-20210501165019000](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501165020.png)

题目中提到了滑动窗口，我们先以示例为例看下什么是滑动窗口？
在示例中，我们从数组中第一个元素开始遍历，由于窗口的大小是3，因此当遍历到第三个元素时，窗口就形成了。

![队列LeetCode239动画01.gif](https://pic.leetcode-cn.com/1616165182-BFfzfI-%E9%98%9F%E5%88%97LeetCode239%E5%8A%A8%E7%94%BB01.gif)

之后，继续遍历元素时，为了保持窗口的大小为3，左侧元素就需要从窗口中剔除。这样使得窗口一直在向右移动，直到考察到最后一个元素结束，这就是所谓的滑动窗口。

![队列LeetCode239窗口滑动.gif](https://pic.leetcode-cn.com/1616165194-uAmNWo-%E9%98%9F%E5%88%97LeetCode239%E7%AA%97%E5%8F%A3%E6%BB%91%E5%8A%A8.gif)

△ 窗口滑动

在上述滑动窗口形成及移动的过程中，我们注意到元素是从窗口的右侧进入的，然后由于窗口大小是固定的，因此多余的元素是从窗口左侧移除的。 一端进入，另一端移除，这不就是队列的性质吗？所以，该题目可以借助队列来求解。

题目要求是返回每个窗口中的最大值。那么这个如何解决呢？

我们以数组{5, 3, 4, 1}，窗口大小k=3来进行说明。这里我们规定窗口右侧边界为right，左侧边界为left，其值为元素下标。

然后，开始遍历nums = {5, 3, 4, 1}。当right指向第一个元素5时，此时队列为空，将第一个元素5入队。

![队列LeetCode239元素5入队.gif](https://pic.leetcode-cn.com/1616165209-TOaHPN-%E9%98%9F%E5%88%97LeetCode239%E5%85%83%E7%B4%A05%E5%85%A5%E9%98%9F.gif)

△ 元素5入队

继续考察第二个元素3，此时3小于队列末尾的元素5，因此元素3入队，以便看其是否是下一个窗口的最大值。这时队列中元素从队首到队尾是递减的。

![队列LeetCode239元素3入队.gif](https://pic.leetcode-cn.com/1616165220-oluKYK-%E9%98%9F%E5%88%97LeetCode239%E5%85%83%E7%B4%A03%E5%85%A5%E9%98%9F.gif)

△ 元素3入队

接着考察{5, 3, 4, 1}中的第三个元素4，此时4大于队尾元素3，这表明元素3不可能是窗口「5 3 4」中的最大元素，因此需要将其从队列移除。但队首有元素5存在，因此不能将其从队首移除，那怎么办呢？我们可以将其从队尾移除。

对于这种一端既可以有元素入队，又有元素出队的队列，称之为双向队列。

队尾元素3出队之后，由于新的队尾元素5大于当前考察元素4，因此元素4入队，以便看其是否是下一个窗口的最大值。

当元素4入队之后，我们发现这时，队列中元素从队首到队尾依旧是递减的。我们把从队首到队尾单调递减或递增的队列称之为单调队列。

![队列LeetCode2393出队4入队.gif](https://pic.leetcode-cn.com/1616165245-fudiUe-%E9%98%9F%E5%88%97LeetCode2393%E5%87%BA%E9%98%9F4%E5%85%A5%E9%98%9F.gif)

△ 元素3出队，元素4入队

接着，考察第四个元素1，此时元素1小于队尾元素4，因此元素1入队。

![队列LeetCode2391入队.gif](https://pic.leetcode-cn.com/1616165259-TndwBb-%E9%98%9F%E5%88%97LeetCode2391%E5%85%A5%E9%98%9F.gif)

△ 元素1入队

但这时，窗口内有4个元素，而我们这里规定窗口大小是3，因此，需要缩小窗口左边界left。

在缩小窗口左边界left后，意味着元素5已经不再窗口内了，因此，队首元素5需要出队。也就是说，当队首元素在原数组中的下标小于窗口左边界时，队首元素就需要移除队列。

![队列LeetCode239缩小边界5出队.gif](https://pic.leetcode-cn.com/1616165277-mgNMnU-%E9%98%9F%E5%88%97LeetCode239%E7%BC%A9%E5%B0%8F%E8%BE%B9%E7%95%8C5%E5%87%BA%E9%98%9F.gif)

△ 缩小左边界left，队首元素5出队

至此，该题目的求解思路就清晰了，具体如下：

遍历给定数组中的元素，如果队列不为空且当前考察元素大于等于队尾元素，则将队尾元素移除。直到，队列为空或当前考察元素小于新的队尾元素；
当队首元素的下标小于滑动窗口左侧边界left时，表示队首元素已经不再滑动窗口内，因此将其从队首移除。
由于数组下标从0开始，因此当窗口右边界right+1大于等于窗口大小k时，意味着窗口形成。此时，队首元素就是该窗口内的最大值。
动画演示

![image-20210501164737449](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501164738.png)

![img](https://pic.leetcode-cn.com/1600878237-EsFWhx-Picture2.png)

![img](https://pic.leetcode-cn.com/1600878237-EYkUHE-Picture3.png)

![img](https://pic.leetcode-cn.com/1600878237-YoQeRX-Picture4.png)

![img](https://pic.leetcode-cn.com/1600878237-cFWnrv-Picture5.png)

![img](https://pic.leetcode-cn.com/1600878237-jrguEx-Picture6.png)

![img](https://pic.leetcode-cn.com/1600878237-NCrTNi-Picture7.png)

###### 代码

需要说明的一点是，在上述动画演示中，队列存储的是元素值。而在具体代码实现中，为了方便计算，队列中**存储的是元素的下标**，详细注释如下：

```java
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0 || k == 0) return new int[0];
        // 窗口个数
        int[] res = new int[nums.length - k + 1];
        LinkedList<Integer> queue = new LinkedList<>();

        // 遍历数组中元素，right表示滑动窗口右边界
        for(int right = 0; right < nums.length; right++) {
            // 如果队列不为空且当前考察元素大于等于队尾元素，则将队尾元素移除。
            // 直到，队列为空或当前考察元素小于新的队尾元素
            while (!queue.isEmpty() && nums[right] >= nums[queue.peekLast()]) {
                queue.removeLast();
            }

            // 存储元素下标
            queue.addLast(right);

            // 计算窗口左侧边界
            int left = right - k +1;
            // 当队首元素的下标小于滑动窗口左侧边界left时
            // 表示队首元素已经不再滑动窗口内，因此将其从队首移除
            if (queue.peekFirst() < left) {
                queue.removeFirst();
            }

            // 由于数组下标从0开始，因此当窗口右边界right+1大于等于窗口大小k时
            // 意味着窗口形成。此时，队首元素就是该窗口内的最大值
            if (right +1 >= k) {
                //当right达到第一个k时，left=0，后面一直到right达到数组尾巴，形成了全部的
                //窗口，left跟结果的索引是一样的，记住队列中是索引，取出来后要再去数组中取值
                res[left] = nums[queue.peekFirst()];
            }
        }
        return res;
    }

作者：hardcore-aryabhata
链接：https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/solution/dong-hua-yan-shi-dan-diao-dui-lie-jian-z-unpy/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210501163939861](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501163941.png)

###### 复杂度分析

![image-20210501162902460](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501162940.png)

![image-20210501164841442](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501164842.png)

## 168、[59 - II. 队列的最大值](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/)

![image-20210501165507881](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501165508.png)

### 思路

#### （1）单调双向队列

![image-20210501170906445](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501170907.png)

![image-20210501171011353](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501171012.png)

![image-20210501171112461](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501171113.png)

![img](https://pic.leetcode-cn.com/1609261619-jyPPLT-Picture3.png)

![img](https://pic.leetcode-cn.com/1609261619-bCHZki-Picture4.png)

![img](https://pic.leetcode-cn.com/1609261619-VJHbWU-Picture5.png)

![img](https://pic.leetcode-cn.com/1609261757-CwSwSi-Picture6.png)

![img](https://pic.leetcode-cn.com/1609261619-TeDGxf-Picture7.png)

![img](https://pic.leetcode-cn.com/1609261619-xvlryq-Picture8.png)

![img](https://pic.leetcode-cn.com/1609261619-ARzNSA-Picture9.png)

![img](https://pic.leetcode-cn.com/1609261619-UZBWSp-Picture10.png)

![img](https://pic.leetcode-cn.com/1609261619-CiZXVu-Picture11.png)



###### 代码

```java
class MaxQueue {
    Queue<Integer> queue;
    Deque<Integer> deque;
    public MaxQueue() {
        queue = new LinkedList<>();
        deque = new LinkedList<>();
    }
    public int max_value() {
        return deque.isEmpty() ? -1 : deque.peekFirst();
    }
    public void push_back(int value) {
        queue.offer(value);
        //注意：这里第二个判断条件不能带等号，即max中对于当前queue中的具有相同值的元素会全部存		//储，而不是存储最近的那个。
        while(!deque.isEmpty() && deque.peekLast() < value)
            deque.pollLast();
        deque.offerLast(value);
    }
    public int pop_front() {
        if(queue.isEmpty()) return -1;
        //Integer类型的值的比较不能直接使用==
        if(queue.peek().equals(deque.peekFirst()))
            deque.pollFirst();
        return queue.poll();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/solution/jian-zhi-offer-59-ii-dui-lie-de-zui-da-z-0pap/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210501171356699](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501171358.png)

![image-20210501172136348](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501172137.png)

#### （2）暴力法普通队列

```java
class MaxQueue {
    int[] q = new int[20000];
    int begin = 0, end = 0;

    public MaxQueue() {

    }
    
    public int max_value() {
        int ans = -1;
        for (int i = begin; i != end; ++i) {
            ans = Math.max(ans, q[i]);
        }
        return ans;
    }
    
    public void push_back(int value) {
        q[end++] = value;
    }
    
    public int pop_front() {
        if (begin == end) {
            return -1;
        }
        return q[begin++];
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/solution/mian-shi-ti-59-ii-dui-lie-de-zui-da-zhi-by-leetcod/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210501172317514](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501172318.png)

#### （3）手撸双端队列

存储单调递减的元素的队列需要是双向队列
如果面试官不允许用STL怎么办呢？自己手撸两个队列，解法如下。
解法2：提升解法
【数据结构】

数组
【思路】

使用head和tail标志，用两个数组模拟队列
由题目的限制条件选择数组长度

```java
public class MaxQueue {
    int[] queue;
    int queueHead;
    int queueTail;
    int[] maxQueue;
    int maxQueueHead;
    int maxQueueTail;
    public MaxQueue() {
        queue=new int[20005];
        queueHead=0;
        queueTail=0;
        maxQueue=new int[20005];
        maxQueueHead=0;
        maxQueueTail=0;
    }
    public int max_value() {
        if(maxQueueTail==maxQueueHead)
            return -1;
        return maxQueue[maxQueueHead];
    }
    public void push_back(int value) {
        if(queueTail>=20000)
            throw new OutOfMemoryError();
        queue[queueTail++]=value;
        //maxQueueTail一直指向的是待存放的空位置
        while(maxQueueHead!=maxQueueTail && value>maxQueue[maxQueueTail-1])//注意这边是从尾部开始比较的
            maxQueueTail--;
        maxQueue[maxQueueTail++]=value;
    }
    public int pop_front() {
        if(queueHead==queueTail)
            return -1;
        int ans=queue[queueHead];
        if(ans==maxQueue[maxQueueHead])
            maxQueueHead++;
        queueHead++;
        return ans;
    }
}

作者：yuanninesuns
链接：https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/solution/liang-chong-fang-fa-xiang-xi-ti-jie-by-yuanninesun/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 169、[n个骰子的点数](https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/)

![image-20210501185521049](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501185522.png)

### 思路

#### （1）暴力法

![image-20210501212105482](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501212106.png)



#### （2）动态规划

![image-20210501212043081](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210501212044.png)

```java
//先算出n个骰子出现的各种和的次数，再除以总数
public double[] dicesProbability(int n) {
    // 通过二维数组记录骰子投掷的状态
    // 第一维：记录n个骰子
    // 第二维：记录n个骰子所掷出的点数和
    ////表示i个骰子掷出s点的次数
    int dp[][] = new int[n + 1][6 * n + 1];
    // 当只有一个骰子时，只能投出1-6点数，可能掷出的次数都为1
    for (int i = 1; i <= 6; i++) {
        dp[1][i] = 1;
    }
    // 遍历骰子的个数
    for (int i = 2; i <= n; i++) {
        // 遍历投掷了当前骰子后可能会出现的点数之和
        // 例如有3个骰子，那么点数和的最小值为全都掷到1，即3，最大值为全都掷到6，即18
        // 因此可能的点数和范围从[i, i*6]
        for (int s = i; s <= 6 * i; s++) {
            // 表示当前这个骰子可能掷出的点数，即1-6
            for (int j = 1; j <= 6; j++) {
                // s-j：表示在还没有投掷第i个骰子，即投掷了i-1个骰子的点数和
                // i-1：表示当投掷了i-1个骰子都掷到1时，即i-1个骰子点数和的最小值
                // 若当前计算的i-1个骰子的点数和<i-1个骰子的点数和的最小值，则这种情况时不合法的，抛弃
                if (s - j < i - 1) {
                    break;
                }
                // 由于s是提前规定的，且s=j+(s-j)
                // 因此，出现 s的次数 由 当前j掷出的点数 与 (i-1)个骰子投出(s-j)点数和出现的次数 决定
                // j可能掷出的点数为1-6，则当前s出现的次数由(i-1)个骰子掷出(s-1)、(s-2)、(s-3)、(s-4)、(s-5)、(s-6)的次数累加决定
                dp[i][s] += dp[i - 1][s - j];
            }
        }
    }
    // 投掷n个骰子，所有点数出现的总次数，有重复
    double total = Math.pow(6, n);
    // 最多只会出现最大值(n*6)-最小值n+最大值本身1，即(n*6)-n+1=n*5+1种点数和
    double[] res = new double[5 * n + 1];
    // 遍历计算概率，即点数出现次数/总次数
    for (int i = n; i <= 6 * n; i++) {
        // 每个点数和出现的概率，从头存放进res数组中
        res[i - n] = ((double) dp[n][i]) / total;
    }
    // 返回结果
    return res;
}
```

## 170、[61. 扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

![image-20210502005943889](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502005945.png)

### 思路

#### （1）Set

![image-20210502010520557](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502010521.png)

![image-20210502010913320](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502010913.png)

###### 代码

```java
class Solution {
    public boolean isStraight(int[] nums) {
        Set<Integer> repeat = new HashSet<>();
        int max = 0, min = 14;
        for(int num : nums) {
            if(num == 0) continue; // 跳过大小王
            max = Math.max(max, num); // 最大牌
            min = Math.min(min, num); // 最小牌
            if(repeat.contains(num)) return false; // 若有重复，提前返回 false
            repeat.add(num); // 添加此牌至 Set
        }
        return max - min < 5; // 最大牌 - 最小牌 < 5 则可构成顺子
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/solution/mian-shi-ti-61-bu-ke-pai-zhong-de-shun-zi-ji-he-se/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210502010904280](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502010905.png)

![image-20210502010842300](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502010843.png)



#### （2）排序

![image-20210502011201938](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502011202.png)

![img](https://pic.leetcode-cn.com/76e8c1f4ba84db93def4e9fdf0e47f0c24dd085acddb72b4efe612fc33ba63e9-Picture9.png)

![img](https://pic.leetcode-cn.com/caa0ad4b11cce1e002a5d4241d3fdab3c1a40eb336acc6d8d0a0e0ffc18ebc48-Picture10.png)

![img](https://pic.leetcode-cn.com/f9a5566708df65d1b58fc6e452ed7b0db9697ece6f8e7b1a5b570d894307a3cd-Picture11.png)

![img](https://pic.leetcode-cn.com/99714ff5a0ae2caca2d7046bb3d1424b25ed84e7035f2a34fba1ab1458f97fe3-Picture12.png)

![img](https://pic.leetcode-cn.com/d91c02409846b472d7847396abf24374b3914f994482a0bfcff6bf05535dedbd-Picture13.png)

![img](https://pic.leetcode-cn.com/630b9d63c642a9814ba0e28b911a8f821d350e597c111652976446417af7691d-Picture14.png)

![img](https://pic.leetcode-cn.com/483ab205c3d326da0e675bae21679233e01620c02bcd8cf044f418654418b5ce-Picture15.png)

```java
class Solution {
    public boolean isStraight(int[] nums) {
        int joker = 0;
        Arrays.sort(nums); // 数组排序
        for(int i = 0; i < 4; i++) {
            if(nums[i] == 0) joker++; // 统计大小王数量
            else if(nums[i] == nums[i + 1]) return false; // 若有重复，提前返回 false
        }
        return nums[4] - nums[joker] < 5; // 最大牌 - 最小牌 < 5 则可构成顺子
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/solution/mian-shi-ti-61-bu-ke-pai-zhong-de-shun-zi-ji-he-se/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502011638183](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502011639.png)

![image-20210502011650198](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502011650.png)

#### （3）位运算

![image-20210502012214906](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502012215.png)

```java
    public boolean isStraight(int[] nums) {
        int bit = 0;//记录每个数字是否出现过
        //记录数组中的最小数字
        int min = Integer.MAX_VALUE;
        //记录数组中的最大数字
        int max = Integer.MIN_VALUE;
        for (int num : nums) {
            //如果是大小王则跳过
            if (num == 0)
                continue;
            //判断相应的位置是否有数字，如果有数字
            //说明之前出现过，也就是有重复的，
            //直接返回false
            if ((bit & (1 << num)) != 0)
                return false;
            //把相应的位置标记为有数字
            bit |= 1 << num;
            //记录遍历过的最大值和最小值
            min = min > num ? num : min;
            max = max < num ? num : max;
        }
        //最大牌和最小牌的差值小于5
        return max - min < 5;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/solution/shu-ju-jie-gou-he-suan-fa-pai-xu-he-wei-7uxg5/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502013403920](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502013404.png)

## 171、[ 62. 圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

![image-20210502013703242](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502013704.png)

### 思路

#### （1）链表

![image-20210502015324895](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502015325.png)

###### 代码

```java
class Solution {
    public int lastRemaining(int n, int m) {
        ArrayList<Integer> list = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            list.add(i);
        }
        int idx = 0;
        while (n > 1) {
            idx = (idx + m - 1) % n;
            list.remove(idx);
            n--;
        }
        return list.get(0);
    }
}

作者：sweetieeyi
链接：https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/javajie-jue-yue-se-fu-huan-wen-ti-gao-su-ni-wei-sh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）数学规律*O*(*n*)

![image-20210502100537924](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502100538.png)

![image-20210502100647849](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502100648.png)

![image-20210502101451422](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502101451.png)



![image-20210502101558244](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502101558.png)



###### 代码

```java
class Solution {
    public int lastRemaining(int n, int m) {
        int ans = 0;
        // 最后一轮剩下2个人，所以从2开始反推
        for (int i = 2; i <= n; i++) {
            ans = (ans + m) % i;
        }
        return ans;
    }
}

作者：sweetieeyi
链接：https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/javajie-jue-yue-se-fu-huan-wen-ti-gao-su-ni-wei-sh/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502100839376](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502100839.png)

#### （3）数学递归

> 官方解答就扔了个公式，还写错了(划掉，官解没错了，因为。。它改了)，然后就得结论了。我们现在来捋一捋这公式到底咋推出来的。
>
> 我们有n个数，下标从0到n-1，然后从`index=0`开始数，每次数m个数，最后看能剩下谁。我们假设能剩下的数的**下标**为y，则我们把这件事表示为
>
> ```
> f(n,m) = y
> ```
>
> 这个y到底表示了啥呢？注意，y是下标，所以就意味着你从`index=0`开始数，数y+1个数，然后就停，停谁身上谁就是结果。
>
> 行了，我们假设`f(n-1,m)=x`，然后来找一找`f(n,m)`和`f(n-1,m)`到底啥关系。
>
> `f(n-1,m)=x`意味着啥呢？意味着有n-1个数的时候从`index=0`开始数，数x+1个数你就找到这结果了。那我不从`index=0`开始数呢？比如我从`index=i`开始数？那很简单，你把上面的答案也往后挪i下，就得到答案了。当然了，你要是挪到末尾了你就取个余，从头接着挪。
>
> 于是我们来思考`f(n,m)`时考虑以下两件事：
>
> 1. 有n个数的时候，要划掉一个数，然后就剩n-1个数了呗，那划掉的这个数，**下标**是多少？
> 2. 划完了这个数，往后数，数x+1个数，停在谁身上谁就是我们的答案。当然了，数的过程中你得取余
>
> **问题一**：有n个数的时候，划掉了谁？**下标**是多少？
>
> 因为要从0数m个数，那最后肯定落到了下标为m-1的数身上了，但这个下标可能超过我们有的最大下标（n-1）了。所以攒**满n个就归零接着数，逢n归零，所以要模n**。
>
> 所以有**n个数**的时候，我们**划掉了下标为`(m-1)%n`**的数字。
>
> **问题二**：我们划完了这个数，往后数x+1下，能落到谁身上呢，它的下标是几？
>
> 你**往后数x+1**，它**下标肯定变成了`(m-1)%n +x+1**`，和第一步的想法一样，你肯定还是得取模，所以答案为`[(m-1)%n+x+1]%n`，则
>
> ```erlang
> f(n,m)=[(m-1)%n+x+1]%n
> ```
>
> 其中`x=f(n-1,m)`
>
> 我们化简它！
>
> 定理一：两个正整数a，b的和，模另外一个数c，就等于它俩分别模c，模完之后加起来再模。
>
> ```perl
> (a+b)%c=((a%c)+(b%c))%c
> ```
>
> 定理二：一个正整数a，模c，模一遍和模两遍是一样的。
>
> ```
> a%c=(a%c)%c
> ```
>
> 你稍微一琢磨就觉得，嗯，说得对。
>
> 所以
>
> ```perl
> f(n,m)=[(m-1)%n+x+1]%n
>       =[(m-1)%n%n+(x+1)%n]%n
>       =[(m-1)%n+(x+1)%n]%n
>       =(m-1+x+1)%n
>       =(m+x)%n
> ```
>
> 剩下的故事你们就都知道了。



![image-20210502104913610](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502104914.png)

![fig1](https://assets.leetcode-cn.com/solution-static/jianzhi_62_fig1.gif)



> [@Lucien](https://leetcode-cn.com/u/lucien_z/) 写得很详细。不过我觉得官方写的公式是对的，官方对f(n,m)的定义是最后留下的元素。f(n-1, m)=x也是最后留下的元素是x，这里上面描述可能有误。然后思考下，n%m是第n%m个元素，不是从0开始的序号，其对应的**真实序号是n%m-1**,所以**n%m恰好是下一个开始的第一个**。**从n%m开始的n-1个元素最终剩下的是x**，与从0开始的n个元素最终剩下的值y之间的关系恰好是y = (m%n + x)%n;

###### 代码

```java
class Solution {
    public int lastRemaining(int n, int m) {
        return f(n, m);
    }

    public int f(int n, int m) {
        if (n == 1) {
            return 0;
        }
        int x = f(n - 1, m);
        return (m + x) % n;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-by-lee/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502105223946](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502105224.png)

![image-20210502105806194](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502105807.png)



## 172、[ 64. 求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)

![image-20210502105914602](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502105914.png)

### 思路

#### （1）逻辑运算符

###### 递归解法

![image-20210502112709962](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502112710.png)



![image-20210502112922104](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502112922.png)



![img](https://pic.leetcode-cn.com/fd3852f5edce37cfb51769e9218c23d9c26713f64d6fa6dae238c5056a80f0b7-Picture3.png)

![img](https://pic.leetcode-cn.com/4370fd929b82be703265b1774c042f1786eb45fcbaeea26a93dd9f1ff39d45cd-Picture4.png)

![img](https://pic.leetcode-cn.com/94b2c9fef8e24081e79f1392d4b28b678bb298295baf873034303cd7504cb880-Picture5.png)

![img](https://pic.leetcode-cn.com/1b5b265b36c767797e3f28235c15df3f34266b858a81ff7738cbc1c8c7cc1685-Picture6.png)

![img](https://pic.leetcode-cn.com/4bde4c6fa429d1678628c0043fb3dde636f08730bc7f81007411ea42cd67462e-Picture7.png)

![img](https://pic.leetcode-cn.com/0b1afff52769f3014431e5f358cf04c356a368bb4d25ccb6c27f22f34683976e-Picture8.png)



![img](https://pic.leetcode-cn.com/2703a0a035a2bda121d229153b7446e6d8e252a008b07d6a1072c18db70a7f4e-Picture9.png)



![img](https://pic.leetcode-cn.com/bc008a136e3ac5c4c1ce099c28eebd2bf2c26d417c2e890ace431287812c0655-Picture10.png)

![img](https://pic.leetcode-cn.com/475d5928ae35e4c1390fe58261bc583fe655dceece1fa69e65a44e8f4fd1f93e-Picture11.png)



![image-20210502113204467](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502113204.png)

###### 代码

```java
class Solution {
    int res = 0;
    public int sumNums(int n) {
        boolean x = n > 1 && sumNums(n - 1) > 0;
        res += n;
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/qiu-12n-lcof/solution/mian-shi-ti-64-qiu-1-2-nluo-ji-fu-duan-lu-qing-xi-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
class Solution {
    public int sumNums(int n) {
        //当n=1的时候，表达式判断n>1不满足直接跳过，返回n=1给上一层，再去累加
        boolean x = n > 1 && (n += sumNums(n - 1)) > 0;
        return n;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/qiu-12n-lcof/solution/mian-shi-ti-64-qiu-1-2-nluo-ji-fu-duan-lu-qing-xi-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210502113006689](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502113007.png)

#### （2）位运算

这个思路不但没用if这种条件判断语句，连逻辑运算中的短路运算也没有用到，也就是连判断的语义也没用到，确实挺牛的，还好这个题限制了n的大小，即限制了n二进制的位数，不然得累加32次……我在代码里为了可读性用临时变量保存了每一位的累加求解，当然一个return也是没问题的。

```java
class Solution {
    /**
     * 负数在参与位运算时使用的是补码
     * -1的原码是   10000000 00000000 00000000 00000001
     * -1的反码是   11111111 11111111 11111111 11111110
     * -1的补码是   11111111 11111111 11111111 11111111
     * 因此任何数与-1做与运算的结果任然为原数
     */
    public int sumNums(int n) {
        /**
         * 由等差数列求和公式可知，结果等于n*(n+1)/2，其中除以2可以通过右移1位进行操作
         * 但n*(n+1)在不允许使用乘法的情况下，只能把n或n+1其中一个拆解为2的n次幂数之和，配合另一个来进行位运算和累加
         * 此代码利用了-1和任何整数进行与运算还等于原数的特点
         * -(n + 1 >> 0 & 1)用于求从低到高第i+1位如果为0取，如果为1取-1
         */
        int n1 = (n & -(n + 1 >> 0 & 1)) << 0;
        int n2 = (n & -(n + 1 >> 1 & 1)) << 1;
        int n3 = (n & -(n + 1 >> 2 & 1)) << 2;
        int n4 = (n & -(n + 1 >> 3 & 1)) << 3;
        int n5 = (n & -(n + 1 >> 4 & 1)) << 4;
        int n6 = (n & -(n + 1 >> 5 & 1)) << 5;
        int n7 = (n & -(n + 1 >> 6 & 1)) << 6;
        int n8 = (n & -(n + 1 >> 7 & 1)) << 7;
        int n9 = (n & -(n + 1 >> 8 & 1)) << 8;
        int n10 = (n & -(n + 1 >> 9 & 1)) << 9;
        int n11 = (n & -(n + 1 >> 10 & 1)) << 10;
        int n12 = (n & -(n + 1 >> 11 & 1)) << 11;
        int n13 = (n & -(n + 1 >> 12 & 1)) << 12;
        int n14 = (n & -(n + 1 >> 13 & 1)) << 13;
        return (n1 + n2 + n3 + n4 + n5 + n6 + n7 + n8 + n9 + n10 + n11 + n12 + n13 + n14) >> 1;
    }
}

作者：shitsurei
链接：https://leetcode-cn.com/problems/qiu-12n-lcof/solution/yan-jiu-liao-ban-tian-zhong-yu-kan-dong-da-lao-de-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 173、[65. 不用加减乘除做加法](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

![image-20210502120847360](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502120847.png)

### 思路

#### （1）位运算

![image-20210502152702820](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502152703.png)

![image-20210502152712122](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502152712.png)

![image-20210502152719958](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502152720.png)



![image-20210502121427122](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502121427.png)

![image-20210502121451763](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502121452.png)

![img](https://pic.leetcode-cn.com/d8f7b12858886ecc73165f0f4b07849e264bdc3c662835d845d14ccbff42a28f-Picture2.png)

![img](https://pic.leetcode-cn.com/7b793038c4ef2263888c8caf763328db667438959674a89003fed68c56c5dbac-Picture3.png)

![img](https://pic.leetcode-cn.com/b67b0ebd864c44a0240b4933eed7247705650bd6f353f541432db023b98a438f-Picture4.png)

![img](https://pic.leetcode-cn.com/d07919f47d9da7550711882da75c9701c0a49bdbee55e7f8f712fafdb84b9464-Picture5.png)

![img](https://pic.leetcode-cn.com/bf214dcad3e25f92477ab46c7904f5104942ec934732a439e82c394529f12f54-Picture6.png)

###### 代码

```java
public int add(int a, int b) {
	while (b != 0) {
		int tempSum = a ^ b;
		int carrySum = (a & b) << 1;
		a = tempSum;
		b = carrySum;
	}
	return a;
}

作者：fakerleet
链接：https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/solution/jin-zhi-tao-wa-ru-he-yong-wei-yun-suan-wan-cheng-j/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



```java
class Solution {
    public int add(int a, int b) {
        while(b != 0) { // 当进位为 0 时跳出
            int c = (a & b) << 1;  // c = 进位
            a ^= b; // a = 非进位和
            b = c; // b = 进位
        }
        return a;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/solution/mian-shi-ti-65-bu-yong-jia-jian-cheng-chu-zuo-ji-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502121828809](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502121829.png)

![image-20210502122247248](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502122247.png)

## 174、[68 - I. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

![image-20210502152832050](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502152832.png)

### 思路

![image-20210502154001599](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502154001.png)



#### （1）迭代

![image-20210502154318546](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502154318.png)

![img](https://pic.leetcode-cn.com/f66371f5682d214f0fde7957e754e98d70a81e6f7182843fa274411dca632de2-Picture3.png)

![img](https://pic.leetcode-cn.com/2f21fc1821859d8cdd28d2c0edacab7d30121292d6c9112d9ab9a56dc6932445-Picture4.png)

![img](https://pic.leetcode-cn.com/f6a8b6d148fc1e223991f8b558bdd42719e58c8cfb131aaac0fbb8c41e4026b1-Picture5.png)

![img](https://pic.leetcode-cn.com/ed42815f9c30b0b7a51cf8f35ed0011de8b773b096ad9fa8fe4fc710747fa9c5-Picture6.png)

###### 代码

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while(root != null) {
            if(root.val < p.val && root.val < q.val) // p,q 都在 root 的右子树中
                root = root.right; // 遍历至右子节点
            else if(root.val > p.val && root.val > q.val) // p,q 都在 root 的左子树中
                root = root.left; // 遍历至左子节点
            else break;
        }
        return root;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/solution/mian-shi-ti-68-i-er-cha-sou-suo-shu-de-zui-jin-g-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210502154604164](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502154604.png)

![image-20210502154621722](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502154622.png)

#### （2）迭代优化

优化：若可保证 p.val < q.val*p*.*v**a**l*<*q*.*v**a**l* ，则在循环中可减少判断条件。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(p.val > q.val) { // 保证 p.val < q.val
            TreeNode tmp = p;
            p = q;
            q = tmp;
        }
        while(root != null) {
            if(root.val < p.val) // p,q 都在 root 的右子树中
                root = root.right; // 遍历至右子节点
            else if(root.val > q.val) // p,q 都在 root 的左子树中
                root = root.left; // 遍历至左子节点
            else break;
        }
        return root;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/solution/mian-shi-ti-68-i-er-cha-sou-suo-shu-de-zui-jin-g-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210502154859809](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502154900.png)



#### （3）递归

![image-20210502155045270](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502155045.png)

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //这里是一直递归到最近公共祖先了，然后把这个结点一层层的返回了
        if(root.val < p.val && root.val < q.val)
            return lowestCommonAncestor(root.right, p, q);
        if(root.val > p.val && root.val > q.val)
            return lowestCommonAncestor(root.left, p, q);
        return root;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/solution/mian-shi-ti-68-i-er-cha-sou-suo-shu-de-zui-jin-g-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502155126193](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502155126.png)

![image-20210502155426916](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502155427.png)



## 175、[68 - II. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

![image-20210502155942331](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502155942.png)

### 思路

#### （1）后序遍历递归

###### 代码

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null) return null; // 如果树为空，直接返回null
        if(root == p || root == q) return root; // 如果 p和q中有等于 root的，那么它们的最近公共祖先即为root（一个节点也可以是它自己的祖先）
        TreeNode left = lowestCommonAncestor(root.left, p, q); // 递归遍历左子树，只要在左子树中找到了p或q，则先找到谁就返回谁
        TreeNode right = lowestCommonAncestor(root.right, p, q); // 递归遍历右子树，只要在右子树中找到了p或q，则先找到谁就返回谁
        if(left == null) return right; // 如果在左子树中 p和 q都找不到，则 p和 q一定都在右子树中，右子树中先遍历到的那个就是最近公共祖先（一个节点也可以是它自己的祖先）
        else if(right == null) return left; // 否则，如果 left不为空，在左子树中有找到节点（p或q），这时候要再判断一下右子树中的情况，如果在右子树中，p和q都找不到，则 p和q一定都在左子树中，左子树中先遍历到的那个就是最近公共祖先（一个节点也可以是它自己的祖先）
        else return root; //否则，当 left和 right均不为空时，说明 p、q节点分别在 root异侧, 最近公共祖先即为 root
    }
}
```

![image-20210502161401034](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502161401.png)

![img](https://pic.leetcode-cn.com/c44f8946548954a2513f7d72e20be260c36c157b506749c788afce1e7bd3416c-Picture1.png)

![img](https://pic.leetcode-cn.com/55f7683ceee27def129a50c9a26305e56b25175dbd3da55983b5848145559354-Picture2.png)

![img](https://pic.leetcode-cn.com/0c3217c102953090030aec857ef2e1e96672b38c450a90356a3e64f0dfc97af2-Picture3.png)

![img](https://pic.leetcode-cn.com/f137a75004bf105ae2f9d2987d3d75c0d0cbddfda54126e549b5a3a99b06a6ef-Picture4.png)

![img](https://pic.leetcode-cn.com/3334d8bc74cad490584a03ca6e6637f4d431626f75ca589710d6a382fe9ab06b-Picture5.png)

![img](https://pic.leetcode-cn.com/fd6ef030cf8acac250792828c04df471ccab669d4153b49b934bc4cc3517efcf-Picture6.png)

![img](https://pic.leetcode-cn.com/e03f2505635e77816e12bdfd2ce5c1c4ace3d2dfa2a0e10eefe683e11e88c98b-Picture7.png)

![img](https://pic.leetcode-cn.com/a9cf21e0a271c74af5ab00e39da09d485de8a3dabbfaa6d6cd2a2a1c7f60d2a8-Picture8.png)

![img](https://pic.leetcode-cn.com/6540e7106efa4461cb19c21a682e9b7c9bd33367d6c5a8bd97982cc7bcec9ec3-Picture9.png)

![img](https://pic.leetcode-cn.com/d249c4379aee12e4a5bce4f20c4ad8b709ff35e358cfdb3255ed6d9c6dc4ae2c-Picture10.png)

![img](https://pic.leetcode-cn.com/fa87c46c8e8360cf3ab5ea852161ab5f9e4a2ca1b5ff6cd9e9ba0897dcbd455a-Picture11.png)

![img](https://pic.leetcode-cn.com/91287818231c969dcc8c69ac9c79197a9b29085d120b18b5230dda77d092f6d6-Picture12.png)

![img](https://pic.leetcode-cn.com/bf71136a0329cf5d48933cb7dc1d8c1bd0cec96c1ad13bcca97cea2f58d14fb5-Picture13.png)

![img](https://pic.leetcode-cn.com/68aced35d03027033c2552b35d477d700e38e7c01f8c1fa76b4cf8b0a1858d30-Picture14.png)

![img](https://pic.leetcode-cn.com/ddf6279a32122924d3608ad7fcdf3f518091da353a1961c0e0e1afaf51d09623-Picture15.png)

![img](https://pic.leetcode-cn.com/b4777e4a6ff72ed49356e20a0a897fd866bb3e4dfdb5e0c8fc1dc0f918029237-Picture16.png)

![img](https://pic.leetcode-cn.com/df510a1fe4750116a935e61ef63ad30a5092bfefe38845497ff9431b3656a793-Picture17.png)

![img](https://pic.leetcode-cn.com/0724b87055c4bc4d744ab64775e6eefa348777c0ea0b07a00ff917773f4b494e-Picture18.png)

###### 复杂度分析

![image-20210502161705427](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502161706.png)

![image-20210502161944186](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502161944.png)

#### （2）非递归

```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //记录遍历到的每个节点的父节点。
        Map<TreeNode, TreeNode> parent = new HashMap<>();
        Queue<TreeNode> queue = new LinkedList<>();
        parent.put(root, null);//根节点没有父节点，所以为空
        queue.add(root);
        //直到两个节点都找到为止。
        while (!parent.containsKey(p) || !parent.containsKey(q)) {
            //队列是一边进一边出，这里poll方法是出队，
            TreeNode node = queue.poll();
            if (node.left != null) {
                //左子节点不为空，记录下他的父节点
                parent.put(node.left, node);
                //左子节点不为空，把它加入到队列中
                queue.add(node.left);
            }
            //右节点同上
            if (node.right != null) {
                parent.put(node.right, node);
                queue.add(node.right);
            }
        }
        Set<TreeNode> ancestors = new HashSet<>();
        //记录下p和他的祖先节点，从p节点开始一直到根节点。
        while (p != null) {
            ancestors.add(p);
            p = parent.get(p);
        }
        //查看p和他的祖先节点是否包含q节点，如果不包含再看是否包含q的父节点……
        while (!ancestors.contains(q))
            q = parent.get(q);
        return q;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/solution/javadai-ma-di-gui-he-fei-di-gui-tu-wen-xiang-jie-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 176、[66. 构建乘积数组](https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/)

![image-20210502164005414](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502164005.png)

### 思路

#### （1）对称遍历

![image-20210502172803510](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502172803.png)

![img](https://pic.leetcode-cn.com/c7ea5883f9b32ba64266ef4d94606741b08a4ccc330ff04447ae4a1061ae031a-Picture2.png)

![img](https://pic.leetcode-cn.com/ebea39d7520e98d5a97e50ce77d8da69ab444b83f2c6d7aeffc21b469047496a-Picture3.png)

![img](https://pic.leetcode-cn.com/473db657677c09301cae12f7995739c15cac0227b3dc44c64fb28b4928107a04-Picture4.png)

![img](https://pic.leetcode-cn.com/fd13ea2b5ade46698336127e6e46e25cb15f1eca69f3b6f5c90a13657fdac6b0-Picture5.png)

![img](https://pic.leetcode-cn.com/5d66de3655a61874a44b078bcde8b774cdc2fcbb09a9cb218021897f82222442-Picture6.png)

![img](https://pic.leetcode-cn.com/e17aaed66a85888a572f054e36ed6d05f1667ac92b114cce38ce2adea6241aac-Picture7.png)

![img](https://pic.leetcode-cn.com/8d969c44a9d606705a5b58915f790dc79efd6f0ab959fc3f2f994e41adedc7c0-Picture8.png)

![img](https://pic.leetcode-cn.com/230c4513737a4dbb39016547e64ac3f656eb8335653190aa38d7950d8302b816-Picture9.png)

![img](https://pic.leetcode-cn.com/5be315eedb69c2b41019cc5a670b1ff82c1f7a1cf783722446ab1fa6d5ea6ad1-Picture10.png)

![img](https://pic.leetcode-cn.com/8114108c5848c8ef4448284c1820e7ed851e6f44c429f205e1e61d73cb811e34-Picture11.png)

###### 代码

题目要求不能使用除法，所以你求出所有的数的乘积，然后再除以每个数构结果集的第一想法就流产了；

这道题就是要求结果数组上的每一个数，都是原数组除了这个位置的数的乘积；

其实本质就是两个dp数组，分别维护 i 左侧、右侧的乘积和。楼主这里是省去了O(n)的空间



![image-20210502212506760](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502212507.png)

![image-20210502212552003](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502212552.png)



```java
public int[] constructArr(int[] a) {
    //边界条件判断
    if (a == null || a.length == 0)
        return a;
    int length = a.length;
    //每个元素左边所有元素的乘积
    int[] resLeft = new int[length];
    //每个元素右边所有元素的乘积
    int[] resRight = new int[length];
    //两个默认值
    resLeft[0] = 1;
    resRight[length - 1] = 1;

    //当前元素左边的所有元素乘积（不包含当前元素），比如当前i=3,那就是0，1,2位置的乘积
    for (int i = 1; i < length; i++) {
        resLeft[i] = resLeft[i - 1] * a[i - 1];
    }
    //当前元素右边的所有元素乘积（不包含当前元素）
    for (int i = length - 2; i >= 0; i--) {
        resRight[i] = resRight[i + 1] * a[i + 1];
    }
    //左边乘以右边就是我们要求的结果
    int[] res = new int[length];
    for (int i = 0; i < length; i++) {
        res[i] = resLeft[i] * resRight[i];
    }
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/solution/zuo-bian-de-cheng-ji-cheng-yi-you-bian-d-wouy/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210502173741660](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502173742.png)

#### （2）优化

```java
class Solution {
    public int[] constructArr(int[] a) {
        if(a.length == 0) return new int[0];
        int[] b = new int[a.length];
        b[0] = 1;
        int tmp = 1;
         //当前元素左边的所有元素乘积（不包含当前元素）
        for(int i = 1; i < a.length; i++) {
            b[i] = b[i - 1] * a[i - 1];
        }
        for(int i = a.length - 2; i >= 0; i--) {
            tmp *= a[i + 1];
            //取出左边数组这个时候的乘积，直接乘以右边数组这个时候的乘积，得到当前位置的值
            b[i] *= tmp;
        }
        return b;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/solution/mian-shi-ti-66-gou-jian-cheng-ji-shu-zu-biao-ge-fe/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210502213729562](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502213729.png)

###### 复杂度分析

![image-20210502213757723](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502213758.png)

## 177、[37. 序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)

![image-20210502213908471](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502213909.png)

### 思路

#### （1）BFS

![image-20210502214450407](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502214450.png)

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if(root == null){
            return "";
        }
        StringBuilder res = new StringBuilder();
        res.append("[");
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if(node != null){
                res.append("" + node.val);
                queue.offer(node.left);
                queue.offer(node.right);
            }else{
                res.append("null");
            }
            res.append(",");
        }
        res.deleteCharAt(res.length() - 1);
        res.append("]");
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if(data == ""){
            return null;
        }
        String[] dataList = data.substring(1, data.length() - 1).split(",");
        TreeNode root = new TreeNode(Integer.parseInt(dataList[0]));
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int i = 1;
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if(!"null".equals(dataList[i])){
                node.left = new TreeNode(Integer.parseInt(dataList[i]));
                queue.offer(node.left);
            }
            i++;
            if(!"null".equals(dataList[i])){
                node.right = new TreeNode(Integer.parseInt(dataList[i]));
                queue.offer(node.right);
            }
            i++;
        }
        return root;
    }
}

作者：edelweisskoko
链接：https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/solution/jian-zhi-offer-37-xu-lie-hua-er-cha-shu-zgixr/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502220842236](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502220843.png)

![image-20210502221034243](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502221035.png)

#### （2）递归

![image-20210502222143614](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502222144.png)

```java
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if(root == null){
            return "null";
        }
        return root.val + "," + serialize(root.left) + "," + serialize(root.right);  
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
        return dfs(queue);
    }

    private TreeNode dfs(Queue<String> queue) {
        String val = queue.poll();
        if("null".equals(val)){
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(val));
        root.left = dfs(queue);
        root.right = dfs(queue);
        return root;
    }
}

作者：edelweisskoko
链接：https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/solution/jian-zhi-offer-37-xu-lie-hua-er-cha-shu-zgixr/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502222212209](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502222213.png)

![image-20210502222418348](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502222419.png)

## 178、[数据流中的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

![image-20210502222901124](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502222901.png)

### 思路

#### （1）堆

![image-20210502231436864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502231437.png)

![image-20210502231631060](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502231631.png)

![img](https://pic.leetcode-cn.com/5b4cb63f90f9c251db8618dff9b5069ecb0e645c391293fd94d0e7604ae15dca-Picture2.png)

![img](https://pic.leetcode-cn.com/d9715b3a6cae6d0f60813611625357e1c47c58a23f64741d7a60acb7c14855fc-Picture3.png)

![img](https://pic.leetcode-cn.com/2126c17a71dea7a623ebbaeed1ffbb1aad8bd6ab60e3e9a2d0e0d7dbf1724f98-Picture4.png)

![img](https://pic.leetcode-cn.com/c2adafcbbbdd55ac020d550bb5dd318213c64f618c934bd6d2f92ffc716fda92-Picture5.png)

![img](https://pic.leetcode-cn.com/f22e746386bc9a57aab0bd9c59eaa8272051d28287e88b9379844f7706c67749-Picture6.png)

![img](https://pic.leetcode-cn.com/c30881523513328ffca3284594c5c62053ca019191f1a18d4cb06ec9a508887c-Picture7.png)

![img](https://pic.leetcode-cn.com/0023c4dc2012a43233316442272d6157bc98337a711624e63a26ce9bcf4aba8a-Picture8.png)

![img](https://pic.leetcode-cn.com/ae8a595670ad9806718b33a2aec6696f2e66e05a638770c90bb87b182ace07b3-Picture9.png)

![img](https://pic.leetcode-cn.com/714ad718155f1ee69cf15977f9e6df76785b8a6d65b81437c68c6beee68d1b42-Picture10.png)

![img](https://pic.leetcode-cn.com/f5668310f0170aa83e4e9019ba7feab1d1863bea736b0ee902ae2f681468b932-Picture11.png)

![img](https://pic.leetcode-cn.com/c9b98c70ffefa63984b0edbf132f515e6bba6b4880c8b2a74525d0322ba612d8-Picture12.png)

![img](https://pic.leetcode-cn.com/1fc7ec5dbb0c03fff3a5460025c860249443ff6c65f433de515811b346c90f48-Picture13.png)

![img](https://pic.leetcode-cn.com/ce778733fa007e49236596b7c34a1f8567df4a72cd35bc87cee40fc7beb43488-Picture14.png)



###### 代码

```java
class MedianFinder {
    Queue<Integer> A, B;
    public MedianFinder() {
        A = new PriorityQueue<>(); // 小顶堆，保存较大的一半
        B = new PriorityQueue<>((x, y) -> (y - x)); // 大顶堆，保存较小的一半
    }
    public void addNum(int num) {
        if(A.size() != B.size()) {
            //不等的话，加到B中，因为肯定是A中的多，但是先加到A中，因为这个值可能是较大的值
            //这样把A中最小的值给到B，这样能保证A中的值都比B中的大，即便这个值可能比B中的值都小
            //如果是这样，最后还是A会把这个值还给B，因为A.poll()就是返回A中最小的给B
            A.add(num);
            B.add(A.poll());
        } else {
            //新的数可能是小的数，给到B中
            B.add(num);
            //A要B中最大的数
            A.add(B.poll());
        }
    }
    public double findMedian() {
        return A.size() != B.size() ? A.peek() : (A.peek() + B.peek()) / 2.0;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/solution/mian-shi-ti-41-shu-ju-liu-zhong-de-zhong-wei-shu-y/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210502233101926](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502233102.png)

![image-20210502233128292](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502233129.png)

## 179、[51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

![image-20210502233217292](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210502233218.png)

### 思路

#### （1）归并排序

![image-20210503161938069](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503161938.png)

![image-20210503155419499](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503155422.png)

![image-20210503162030716](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503162031.png)

> 这里说得好乱啊
>
> ```css
> 我们发现用这种「算贡献」的思想在合并的过程中计算逆序对的数量的时候，只在 lPtr 右移的时候计算，是基于这样的事实：
> 当前 lPtr 指向的数字比 rPtr 小，
> 但是比 R 中 [0 ... rPtr - 1] 的其他数字大，[0 ... rPtr - 1] 的其他数字本应当排在 lPtr 对应数字的左边，
> 但是它排在了右边，所以这里就贡献了 rPtr 个逆序对。
> ```
>
> 我觉得：一个是rPtr右移的时候才计算吧。当rPtr指向的元素比lPtr小，这意味着在L中，[lPtr...mid]都比rPtr此时指向的元素都大，贡献了mid-lPtr+1个逆序对。



###### 代码注释

```java
class Solution {
    public int reversePairs(int[] nums) {
        int len = nums.length;
        if(len < 2) return 0;
        int[] copy = new int[len];
        for(int i = 0; i < len; i++) copy[i] = nums[i];
        int[] tmp = new int[len];
        return reversePairs(copy, 0, len - 1, tmp); //方法重载机制
    }

    private int reversePairs(int[] nums, int left, int right, int[] tmp){
        if(left == right) return 0; //递归终结者
        int mid = left + (right - left) / 2;//此处采用此方式是因为(right + left) / 2中的right + left可能产生越界，所谓的分割数组，也不过是定义左右指针不断变小而已，这里的递归就是不断地切分数组，看，mid已经成了下个递归的right指针了
        int leftPairs = reversePairs(nums, left, mid, tmp);
        int rightPairs = reversePairs(nums, mid + 1, right, tmp);
        if(nums[mid] <= nums[mid + 1]) return leftPairs + rightPairs; // 此时，左边子数组的最大数小于右边子数组的最小数，直接合并即可，不会产生逆序对
        int crossPairs = mergerAndCount(nums, left, mid, right, tmp); //crossPairs是将两个有序子数组归并为一个有序数组产生的逆序对
        return crossPairs + rightPairs + leftPairs; 
    }

    private int mergerAndCount(int[] nums, int left, int mid, int right, int[] tmp){
        //全程采用一个数组tmp的原因有二：不必一直创建新的数组，节约资源；每次处理的都是子数组，如果创建新的数组会导致索引的处理很麻烦，容易出错
        //注意这里都得用left和right作为边界，不能用nums.length和0，因为nums是整个数组，但是每次归并排序，都是对其中的一部分进行的排序
        for(int i = left; i <= right; i++) tmp[i] = nums[i];
        int i = left;   //左边的有序数组的左边界
        int j = mid + 1;//右边的有序数组的左边界
        int count = 0;
        for(int k = left; k <= right; k++){
            if(i == mid + 1){
                nums[k] = tmp[j]; //此时左边的子数组长度为0
                j++;
            }else if(j == right + 1){
                nums[k] = tmp[i]; //此时右边的子数组长度为0
                i++;
             //要知道，i和j加mid值把temp数组分割成两个数组了，所以这个是两个子数组的值在比较
            }else if(tmp[i] <= tmp[j]){ //这儿必须得是“<=”，如是“<”，则归并排序是不稳定的
                nums[k] = tmp[i]; //左边子数组的值较小，进入有序数组
                i++;
            }else{
                nums[k] = tmp[j]; //此时，右边子数组的值较小，进入有序数组
                j++;
                //因为此时i指向的值一直到mid的值，都比右子数组的j这个时候指向的值大，所以i和i以			//后的值都跟这个时候的j构成了逆序对
                count += (mid - i + 1);//左边子数组剩余的个数即为逆序对个数
            }
        }
        return count;
    }
}
```

![image-20210503160345126](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503160345.png)

如下图所示，为左子数组 [2, 3, 6, 7][2,3,6,7] 与 右子数组 [0, 1, 4, 5][0,1,4,5] 的合并与逆序对统计过程。

![img](https://pic.leetcode-cn.com/1614274007-YkCQOz-Picture3.png)

![image-20210503160547342](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503160547.png)

![image-20210503160641979](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503160642.png)

![img](https://pic.leetcode-cn.com/1614274007-CiIXuc-Picture6.png)

![image-20210503160746705](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503160747.png)

![img](https://pic.leetcode-cn.com/1614274007-lQuslJ-Picture8.png)

![image-20210503160841684](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503160842.png)

![img](https://pic.leetcode-cn.com/1614274007-OiRvkv-Picture10.png)

![img](https://pic.leetcode-cn.com/1614274007-SaTkTO-Picture11.png)

![img](https://pic.leetcode-cn.com/1614274007-vRSdQI-Picture12.png)

![img](https://pic.leetcode-cn.com/1614274007-ounrkz-Picture13.png)

![img](https://pic.leetcode-cn.com/1614274007-UZQbes-Picture14.png)

![img](https://pic.leetcode-cn.com/1614274007-YlPdBQ-Picture15.png)

![img](https://pic.leetcode-cn.com/1614274007-cCdAyU-Picture16.png)

![image-20210503161052742](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503161053.png)

![img](https://pic.leetcode-cn.com/1614274007-rowvTK-Picture18.png)

![img](https://pic.leetcode-cn.com/1614274007-srjZbc-Picture19.png)

因此，考虑在归并排序的合并阶段统计「逆序对」数量，完成归并排序时，也随之完成所有逆序对的统计。

###### 代码2

```java
class Solution {
    int[] nums, tmp;
    public int reversePairs(int[] nums) {
        this.nums = nums;
        tmp = new int[nums.length];
        return mergeSort(0, nums.length - 1);
    }
    private int mergeSort(int l, int r) {
        // 终止条件
        if (l >= r) return 0;
        // 递归划分
        int m = (l + r) / 2;
        int res = mergeSort(l, m) + mergeSort(m + 1, r);
        // 合并阶段
        int i = l, j = m + 1;
        for (int k = l; k <= r; k++)
            tmp[k] = nums[k];
        for (int k = l; k <= r; k++) {
            if (i == m + 1)
                nums[k] = tmp[j++];
            else if (j == r + 1 || tmp[i] <= tmp[j])
                nums[k] = tmp[i++];
            else {
                nums[k] = tmp[j++];
                res += m - i + 1; // 统计逆序对
            }
        }
        return res;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/jian-zhi-offer-51-shu-zu-zhong-de-ni-xu-pvn2h/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210503163715521](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503163715.png)

![img](https://pic.leetcode-cn.com/066bc0ae3f1c0cf4524eed4fc1efe1e4fb51b5c69a90009c7a3cd422216b5ef9-file_1584437845064)



## 180、[19. 正则表达式匹配](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

![image-20210503164024357](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503164025.png)

![image-20210503164045629](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503164046.png)

### 思路

#### （1）动态规划

![image-20210503180044538](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503180045.png)

结果

我们开数组要开 n+1*n*+1 ，这样对于空串的处理十分方便。结果就是 ![image-20210503180118071](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503180119.png)

###### 代码

```java
class Solution {
    public boolean isMatch(String A, String B) {
        int n = A.length();
        int m = B.length();
        boolean[][] f = new boolean[n + 1][m + 1];

        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                //分成空正则和非空正则两种
                if (j == 0) {
                    f[i][j] = i == 0;
                } else {
                    //非空正则分为两种情况 * 和 非*
                    if (B.charAt(j - 1) != '*') {
                        if (i > 0 && (A.charAt(i - 1) == B.charAt(j - 1) || B.charAt(j - 1) == '.')) {
                            f[i][j] = f[i - 1][j - 1];
                        }
                    } else {
                        //碰到 * 了，分为看和不看两种情况
                        //不看
                        if (j >= 2) {
                            f[i][j] |= f[i][j - 2];
                        }
                        //看，这里的越界判断i，j都要有
                        if (i >= 1 && j >= 2 && (A.charAt(i - 1) == B.charAt(j - 2) || B.charAt(j - 2) == '.')) {
                            f[i][j] |= f[i - 1][j];
                        }
                    }
                }
            }
        }
        return f[n][m];
    }
}

作者：jerry_nju
链接：https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/solution/zhu-xing-xiang-xi-jiang-jie-you-qian-ru-shen-by-je/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210503183441088](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503183441.png)

![image-20210503183728102](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503183728.png)

#### （2）递归

```java
class Solution {
    public boolean isMatch(String A, String B) {
        // 如果字符串长度为0，需要检测下正则串
        if (A.length() == 0) {
            // 如果正则串长度为奇数，必定不匹配，比如 "."、"ab*",必须是 a*b*这种形式，*在奇数位上
            if (B.length() % 2 != 0) return false;
            int i = 1;
            while (i < B.length()) {
                if (B.charAt(i) != '*') return false;
                i += 2;
            }
            return true;
        }
        // 如果字符串长度不为0，但是正则串没了，return false
        if (B.length() == 0) return false;
        // c1 和 c2 分别是两个串的当前位，c3是正则串当前位的后一位，如果存在的话，就更新一下
        char c1 = A.charAt(0), c2 = B.charAt(0), c3 = 'a';
        if (B.length() > 1) {
            c3 = B.charAt(1);
        }
        // 和dp一样，后一位分为是 '*' 和不是 '*' 两种情况
        if (c3 != '*') {
            // 如果该位字符一样，或是正则串该位是 '.',也就是能匹配任意字符，就可以往后走
            if (c1 == c2 || c2 == '.') {
                return isMatch(A.substring(1), B.substring(1));
            } else {
                // 否则不匹配
                return false;
            }
        } else {
            // 如果该位字符一样，或是正则串该位是 '.'，和dp一样，有看和不看两种情况
            if (c1 == c2 || c2 == '.') {
                return isMatch(A.substring(1), B) || isMatch(A, B.substring(2));
            } else {
                // 不一样，那么正则串这两位就废了，直接往后走
                return isMatch(A, B.substring(2));
            }
        }
    }
}

作者：jerry_nju
链接：https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/solution/zhu-xing-xiang-xi-jiang-jie-you-qian-ru-shen-by-je/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



# LeetCode热题100道

## 181、[LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

![image-20210503204323061](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503204323.png)

### 思路

#### （1）哈希表+双向链表

![image-20210503211731429](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503211731.png)

![image-20210503211801349](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503211802.png)

![image-20210503211853633](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503211854.png)

![image-20210503211928810](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503211929.png)

![image-20210503212018214](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503212019.png)

![image-20210503212139710](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503212140.png)

![image-20210503212334490](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503212334.png)

###### 代码

```java
class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // 最大容量
    private int cap;
    
    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if (!map.containsKey(key))
            return -1;
        int val = map.get(key).val;
        // 利用 put 方法把该数据提前
        put(key, val);
        return val;
    }
    
    public void put(int key, int val) {
        // 先把新节点 x 做出来
        Node x = new Node(key, val);
        
        if (map.containsKey(key)) {
            // 删除旧的节点，新的插到头部
            cache.remove(map.get(key));
            cache.addFirst(x);
            // 更新 map 中对应的数据
            map.put(key, x);
        } else {
            if (cap == cache.size()) {
                // 删除链表最后一个数据
                Node last = cache.removeLast();
                map.remove(last.key);
            }
            // 直接添加到头部
            cache.addFirst(x);
            map.put(key, x);
        }
    }
}

作者：labuladong
链接：https://leetcode-cn.com/problems/lru-cache/solution/lru-ce-lue-xiang-jie-he-shi-xian-by-labuladong/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210503212428100](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503212429.png)

###### 解释2

小贴士

> 在双向链表的实现中，使用一个伪头部（dummy head）和伪尾部（dummy tail）标记界限，这样在添加节点和删除节点的时候就不需要检查相邻的节点是否存在。

![img](https://assets.leetcode-cn.com/solution-static/146/1.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/2.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/3.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/4.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/5.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/6.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/7.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/8.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/9.PNG)

![img](https://assets.leetcode-cn.com/solution-static/146/10.PNG)

###### 代码2

```java
public class LRUCache {
    class DLinkedNode {
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;
        public DLinkedNode() {}
        public DLinkedNode(int _key, int _value) {key = _key; value = _value;}
    }

    private Map<Integer, DLinkedNode> cache = new HashMap<Integer, DLinkedNode>();
    private int size;
    private int capacity;
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        // 使用伪头部和伪尾部节点
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        // 如果 key 存在，先通过哈希表定位，再移到头部
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            // 如果 key 不存在，创建一个新的节点
            DLinkedNode newNode = new DLinkedNode(key, value);
            //记得** 添加进哈希表
            cache.put(key, newNode);
            // 添加至双向链表的头部
            addToHead(newNode);
            ++size;
            if (size > capacity) {
                // 如果超出容量，删除双向链表的尾部节点
                DLinkedNode tail = removeTail();
                // 记得**删除哈希表中对应的项
                cache.remove(tail.key);
                --size;
            }
        }
        else {
            // 如果 key 存在，先通过哈希表定位，再修改 value，并移到头部
            node.value = value;
            moveToHead(node);
        }
    }

    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/lru-cache/solution/lruhuan-cun-ji-zhi-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210503213118066](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503213119.png)

## 182、[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

![image-20210503235815808](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210503235816.png)

### 思路

#### （1）原地修改+三指针

![image-20210504000516266](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504000517.png)

![img](https://pic.leetcode-cn.com/1617592243-iFTFCu-1.jpg)

![image-20210504001143972](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504001144.png)

![img](https://pic.leetcode-cn.com/1617592252-NWqDin-4.jpg)

![img](https://pic.leetcode-cn.com/1617592252-NWqDin-4.jpg)

![img](https://pic.leetcode-cn.com/1617592254-dPHsFP-5.jpg)

![img](https://pic.leetcode-cn.com/1617592257-YBlHGM-6.jpg)

![img](https://pic.leetcode-cn.com/1617592260-fIjOxY-7.jpg)

![img](https://pic.leetcode-cn.com/1617592262-ayhhzR-8.jpg)

![img](https://pic.leetcode-cn.com/1617592264-oDsTGl-9.jpg)

![img](https://pic.leetcode-cn.com/1617592266-NGEddv-10.jpg)

![img](https://pic.leetcode-cn.com/1617592268-KnuhxZ-11.jpg)

![img](https://pic.leetcode-cn.com/1617592270-CgHEhX-12.jpg)

![img](https://pic.leetcode-cn.com/1617592272-ELIvKg-13.jpg)

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1 = m - 1, p2 = n - 1;
        int tail = m + n - 1;
        int cur;
        while (p1 >= 0 || p2 >= 0) {
            if (p1 == -1) {
                cur = nums2[p2--];
            } else if (p2 == -1) {
                cur = nums1[p1--];
            } else if (nums1[p1] > nums2[p2]) {
                cur = nums1[p1--];
            } else {
                cur = nums2[p2--];
            }
            nums1[tail--] = cur;
        }
    }
}

作者：demigodliu
链接：https://leetcode-cn.com/problems/merge-sorted-array/solution/ni-xiang-shuang-zhi-zhen-he-bing-liang-g-ucgj/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210504001514879](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504001515.png)

#### （2）双指针

![image-20210504001912346](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504001913.png)

![gif1](https://assets.leetcode-cn.com/solution-static/88/1.gif)

![image-20210504001958364](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504001959.png)

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1 = 0, p2 = 0;
        int[] sorted = new int[m + n];
        int cur;
        while (p1 < m || p2 < n) {
            if (p1 == m) {
                cur = nums2[p2++];
            } else if (p2 == n) {
                cur = nums1[p1++];
            } else if (nums1[p1] < nums2[p2]) {
                cur = nums1[p1++];
            } else {
                cur = nums2[p2++];
            }
            sorted[p1 + p2 - 1] = cur;
        }
        for (int i = 0; i != m + n; ++i) {
            nums1[i] = sorted[i];
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/merge-sorted-array/solution/he-bing-liang-ge-you-xu-shu-zu-by-leetco-rrb0/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504002107300](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504002108.png)

![image-20210504002453968](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504002454.png)

#### （3）直接合并后排序

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for (int i = 0; i != n; ++i) {
            nums1[m + i] = nums2[i];
        }
        Arrays.sort(nums1);
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/merge-sorted-array/solution/he-bing-liang-ge-you-xu-shu-zu-by-leetco-rrb0/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504002610324](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504002611.png)

## 183、[ 验证回文串](https://leetcode-cn.com/problems/valid-palindrome/)

![image-20210504105430705](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504105433.png)

### 思路

#### （1）双指针

![image-20210504105637873](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504105638.png)

###### 代码

```java
    public boolean isPalindrome(String s) {
        if (s.isEmpty())
            return true;
        int left = 0, right = s.length() - 1;
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left)))
                left++;
            while (left < right && !Character.isLetterOrDigit(s.charAt(right)))
                right--;
            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right)))
                return false;
            left++;
            right--;
        }
        return true;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/valid-palindrome/solution/javade-4chong-jie-ti-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504110830749](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504110833.png)

#### （2）双指针迭代

 我们还可以在比较之前字母全部转化为小写，这里改为for循环的方式，只不过是换汤不换药，原理还都是一样的

```java
    public boolean isPalindrome(String s) {
        if (s == null || s.length() == 0)
            return true;
        s = s.toLowerCase();
        for (int i = 0, j = s.length() - 1; i < j; i++, j--) {
            while (i < j && !Character.isLetterOrDigit(s.charAt(i)))
                i++;
            while (i < j && !Character.isLetterOrDigit(s.charAt(j)))
                j--;
            if (s.charAt(i) != s.charAt(j))
                return false;
        }
        return true;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/valid-palindrome/solution/javade-4chong-jie-ti-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504111441929](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504111442.png)

#### （3）正则

我们还可以使用正则匹配，把特殊字符过滤掉，只留下字母和数字，然后转化为小写，再反转，最后在判断是否相等。当然这种写法只是提供了一种思路，面试中如果写出这种肯定还会让你再写一种的，因为它没有完全考察出对这题的解题思路，

```java
    public boolean isPalindrome(String s) {
        String actual = s.replaceAll("[^A-Za-z0-9]", "").toLowerCase();
        String rev = new StringBuffer(actual).reverse().toString();
        return actual.equals(rev);
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/valid-palindrome/solution/javade-4chong-jie-ti-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （4）递归

```java
    public boolean isPalindrome(String s) {
        return isPalindromeHelper(s, 0, s.length() - 1);
    }

    public boolean isPalindromeHelper(String s, int left, int right) {
        if (left >= right)
            return true;
        while (left < right && !Character.isLetterOrDigit(s.charAt(left)))
            left++;
        while (left < right && !Character.isLetterOrDigit(s.charAt(right)))
            right--;
        return Character.toLowerCase(s.charAt(left)) == Character.toLowerCase(s.charAt(right)) && isPalindromeHelper(s, ++left, --right);
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/valid-palindrome/solution/javade-4chong-jie-ti-fang-shi-by-sdwwld/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210504112143463](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504112144.png)

## 184、[数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

![image-20210504114644781](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504114646.png)

### 思路

#### （1）快排暴力解法

![image-20210504120225749](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120227.png)

```java
import java.util.Arrays;

public class Solution {

    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        Arrays.sort(nums);
        return nums[len - k];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504120321500](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120322.png)

#### （2）快排减治

![image-20210504120413291](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120414.png)

![image-20210504120457942](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120458.png)

![image-20210504120517848](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120518.png)

###### 代码

![image-20210504121309356](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504121310.png)

```java
public class Solution {

    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        int left = 0;
        int right = len - 1;

        // 转换一下，第 k 大元素的索引是 len - k
        int target = len - k;

        while (true) {
            int index = partition(nums, left, right);
            if (index == target) {
                return nums[index];
            } else if (index < target) {
                left = index + 1;
            } else {
                right = index - 1;
            }
        }
    }

    /**
     * 在数组 nums 的子区间 [left, right] 执行 partition 操作，返回 nums[left] 排序以后应该在的位置
     * 在遍历过程中保持循环不变量的语义
     * 1、[left + 1, j] < nums[left]
     * 2、(j, i] >= nums[left]
     *
     * @param nums
     * @param left
     * @param right
     * @return
     */
    public int partition(int[] nums, int left, int right) {
        int pivot = nums[left];
        int j = left;
        for (int i = left + 1; i <= right; i++) {
            if (nums[i] < pivot) {
                // 小于 pivot 的元素都被交换到前面
                j++;
                swap(nums, j, i);
            }
        }
        // 在之前遍历的过程中，满足 [left + 1, j] < pivot，并且 (j, i] >= pivot
        swap(nums, j, left);
        // 交换以后 [left, j - 1] < pivot, nums[j] = pivot, [j + 1, right] >= pivot
        return j;
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210504120634876](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504120635.png)

#### （3）快排优化

```java
import java.util.Random;

public class Solution {

    private static Random random = new Random(System.currentTimeMillis());

    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        int target = len - k;
        int left = 0;
        int right = len - 1;
        while (true) {
            int index = partition(nums, left, right);
            if (index < target) {
                left = index + 1;
            } else if (index > target) {
                right = index - 1;
            } else {
                return nums[index];
            }
        }
    }

    // 在区间 [left, right] 这个区间执行 partition 操作

    private int partition(int[] nums, int left, int right) {
        // 在区间随机选择一个元素作为标定点，就是随机选择一个作为最左边的比较值
        if (right > left) {
            int randomIndex = left + 1 + random.nextInt(right - left);
            swap(nums, left, randomIndex);
        }

        int pivot = nums[left];
        int j = left;
        for (int i = left + 1; i <= right; i++) {
            if (nums[i] < pivot) {
                j++;
                swap(nums, j, i);
            }
        }
        swap(nums, left, j);
        return j;
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
} 

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210504145717917](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504145718.png)

#### （4）快排双指针

**快排的另一种写法**，使用头尾的双指针来确定切分元素的位置

使用双指针，将与 `pivot` 相等的元素等概论地分到 `pivot` 最终排定位置的两边。

使用双指针的办法找到切分元素的位置

```java
import java.util.Random;

public class Solution {

    private static Random random = new Random(System.currentTimeMillis());

    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        int left = 0;
        int right = len - 1;

        // 转换一下，第 k 大元素的索引是 len - k
        int target = len - k;

        while (true) {
            int index = partition(nums, left, right);
            if (index == target) {
                return nums[index];
            } else if (index < target) {
                left = index + 1;
            } else {
                right = index - 1;
            }
        }
    }

    public int partition(int[] nums, int left, int right) {
        // 在区间随机选择一个元素作为标定点
        if (right > left) {
            int randomIndex = left + 1 + random.nextInt(right - left);
            swap(nums, left, randomIndex);
        }

        int pivot = nums[left];

        // 将等于 pivot 的元素分散到两边
        // [left, lt) <= pivot
        // (rt, right] >= pivot

        int lt = left + 1;
        int rt = right;

        while (true) {
            while (lt <= rt && nums[lt] < pivot) {
                lt++;
            }
            while (lt <= rt && nums[rt] > pivot) {
                rt--;
            }

            if (lt > rt) {
                break;
            }
            swap(nums, lt, rt);
            lt++;
            rt--;
        }

        swap(nums, left, rt);
        return rt;
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/partitionfen-er-zhi-zhi-you-xian-dui-lie-java-dai-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210504151415449](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504151415.png)

#### （5）优先队列

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> heap = new PriorityQueue<>();
        for (int num : nums) {
            heap.add(num);
            if (heap.size() > k) {
                heap.poll();
            }
        }
        return heap.peek();
    }
}
```

###### 手撸大顶堆实现



![image-20210504160557697](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504160558.png)



![img](https://assets.leetcode-cn.com/solution-static/215/2.png)

![img](https://assets.leetcode-cn.com/solution-static/215/3.png)



![img](https://assets.leetcode-cn.com/solution-static/215/4.png)

![img](https://assets.leetcode-cn.com/solution-static/215/5.png)

![img](https://assets.leetcode-cn.com/solution-static/215/6.png)



![img](https://assets.leetcode-cn.com/solution-static/215/7.png)

![img](https://assets.leetcode-cn.com/solution-static/215/8.png)



![image-20210504160822216](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504160822.png)

![img](https://assets.leetcode-cn.com/solution-static/215/10.png)

![img](https://assets.leetcode-cn.com/solution-static/215/11.png)

![img](https://assets.leetcode-cn.com/solution-static/215/12.png)

![img](https://assets.leetcode-cn.com/solution-static/215/13.png)

![img](https://assets.leetcode-cn.com/solution-static/215/14.png)

![img](https://assets.leetcode-cn.com/solution-static/215/15.png)

![img](https://assets.leetcode-cn.com/solution-static/215/16.png)

![img](https://assets.leetcode-cn.com/solution-static/215/17.png)



![img](https://assets.leetcode-cn.com/solution-static/215/18.png)



![img](https://assets.leetcode-cn.com/solution-static/215/19.png)

![img](https://assets.leetcode-cn.com/solution-static/215/20.png)





###### 代码

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int[] arr = Arrays.copyOf(nums, nums.length);
        int len = arr.length;
        int number = 0;
        /*初建堆*/
        buildHeap(arr, len);
        for(int i = len - 1; i > 0; i--) {
            swap(arr, 0, i);
            number++;
            if(number == k) {
                return arr[i];
            }
            len--;
            heapify(arr, 0, len);
        }
        return arr[0];
    }
    /*因为完全二叉树的特性，Math.floor(len / 2)是最后一个非叶子节点，所以从Math.floor(len / 2)开始。*/
    private void buildHeap(int[] arr, int len) {
        for(int i = (int) Math.floor(len / 2); i >= 0; i--) {
            heapify(arr, i, len);
        }
    }
    /*将i位置元素和其子节点按照大根堆的规则，构建为一个堆结构*/
    private void heapify(int[] arr, int i, int len) {
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        int largest = i;
        if(left < len && arr[left] > arr[largest]) {
            largest = left;
        }
        if(right < len && arr[right] > arr[largest]) {
            largest = right;
        }
        if(largest != i) {
            swap(arr, i, largest);
            //交换后子树可能不是堆结构，构建子树堆结构
            heapify(arr, largest, len);
        }
    }
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int heapSize = nums.length;
        buildMaxHeap(nums, heapSize);
        //要找到第k大，那堆顶要删除k-1个元素，即删k-1次元素，
        for (int i = nums.length - 1; i >= nums.length - k + 1; --i) {
            swap(nums, 0, i);
            --heapSize;
            //去掉最后一位最大的，重新构建堆
            maxHeapify(nums, 0, heapSize);
        }
        return nums[0];
    }

    public void buildMaxHeap(int[] a, int heapSize) {
        for (int i = heapSize / 2; i >= 0; --i) {
            maxHeapify(a, i, heapSize);
        } 
    }

    public void maxHeapify(int[] a, int i, int heapSize) {
        int l = i * 2 + 1, r = i * 2 + 2, largest = i;
        if (l < heapSize && a[l] > a[largest]) {
            largest = l;
        } 
        if (r < heapSize && a[r] > a[largest]) {
            largest = r;
        }
        if (largest != i) {
            swap(a, i, largest);
            maxHeapify(a, largest, heapSize);
        }
    }

    public void swap(int[] a, int i, int j) {
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/shu-zu-zhong-de-di-kge-zui-da-yuan-su-by-leetcode-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210504152042293](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504152042.png)



![image-20210504152202497](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504152202.png)



![image-20210504152010985](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504152011.png)

###### 手撸小顶堆实现

```java
/**
 * 1 ms, 100%
 * 36.9 MB, 94.47%
 */
class Solution2 {

    public int findKthLargest(int[] nums, int k) {
        //前K个元素原地建小顶堆
        buildHeap(nums, k);
        //遍历剩下元素，比堆顶小，跳过；比堆顶大，交换后重新堆化
        for (int i = k; i < nums.length; i++) {
            if (nums[i] < nums[0]) continue;
            swap(nums, i, 0);
            heapify(nums, k, 0);
        }
        //K个元素的小顶堆的堆顶即是第K大元素
        return nums[0];
    }

    /**
        * 建堆函数
        * 从倒数第一个非叶子节点开始堆化，倒数第一个非叶子节点下标为 K/2-1
        */
    public void buildHeap(int[] a, int k) {
        for (int i = k/2 - 1; i >= 0; i--) {
            heapify(a, k, i);
        }
    }

    /**
        * 堆化函数
        * 父节点下标i，左右子节点的下标分别为 2*i+1 和 2*i+2
        */
    public void heapify(int[] a, int k, int i) {
        //临时变量 minPos 用于存储最小值的下标，先假设父节点最小
        int minPos = i;
        while (true) {
            //和左子节点比较
            if (i*2+1 < k && a[i*2+1] < a[i]) minPos = i*2+1;
            //和右子节点比较
            if (i*2+2 < k && a[i*2+2] < a[minPos]) minPos = i*2+2;
            //如果minPos没有发生变化，说明父节点已经是最小了，直接跳出
            if (minPos == i) break;
            //否则交换
            swap(a, i, minPos);
            //父节点下标进行更新，继续堆化
            i = minPos;
        }
    }

    public void swap(int[] a, int n, int m) {
        int tmp = a[n];
        a[n] = a[m];
        a[m] = tmp;
    }

}

作者：yankuangshigo
链接：https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/java-pai-xu-xiao-ding-dui-shi-xian-by-yankuangshig/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 根据大顶堆的代码进行改造小顶堆

```java
class Solution {

    public int findKthLargest(int[] nums, int k) {
         buildTree(nums,k);
        //维持堆的大小为K
         for(int i=k;i<=nums.length-1;i++){
             //因为是求第K大的，所以比堆顶还小的肯定不符合要求
             if(nums[i]<nums[0]) continue;
             //这里交换堆顶的堆里面的最小值和当前的数组的值
             swap(nums,0,i);
             //最后一个设置堆的大小为K，从0位置开始调整堆
             maxHeapHead(nums,0,k);
         }
         return nums[0];
    }


    public void buildTree(int [] nums,int heapSize){
        for(int i=heapSize/2-1;i>=0;i--){
            maxHeapHead(nums,i,heapSize);
        }
    }

    public void maxHeapHead(int [] nums,int i,int heapSize){
        int left = 2*i+1,right=2*i+2,least=i;
        if(left<heapSize && nums[left]<nums[least]) least=left;
        if(right<heapSize &&nums[right]<nums[least]) least=right;
        if(least!=i){
            swap(nums,i,least);
            maxHeapHead(nums,least,heapSize);
        }
    }

    public void swap(int [] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;

    } 
}
```



###### 复杂度分析

![image-20210504171259978](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504171300.png)

![image-20210504171829770](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504171830.png)

## 185、[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

![image-20210504222507716](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210504222512.png)

### 思路

#### （1）暴力合并

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int[] nums;
    int m = nums1.length;
    int n = nums2.length;
    nums = new int[m + n];
    if (m == 0) {
        if (n % 2 == 0) {
            return (nums2[n / 2 - 1] + nums2[n / 2]) / 2.0;
        } else {

            return nums2[n / 2];
        }
    }
    if (n == 0) {
        if (m % 2 == 0) {
            return (nums1[m / 2 - 1] + nums1[m / 2]) / 2.0;
        } else {
            return nums1[m / 2];
        }
    }

    int count = 0;
    int i = 0, j = 0;
    while (count != (m + n)) {
        if (i == m) {
            while (j != n) {
                nums[count++] = nums2[j++];
            }
            break;
        }
        if (j == n) {
            while (i != m) {
                nums[count++] = nums1[i++];
            }
            break;
        }

        if (nums1[i] < nums2[j]) {
            nums[count++] = nums1[i++];
        } else {
            nums[count++] = nums2[j++];
        }
    }

    if (count % 2 == 0) {
        return (nums[count / 2 - 1] + nums[count / 2]) / 2.0;
    } else {
        //注意这里就不要再把元素除以2了，只要返回中间的元素即可
        return nums[count / 2];
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210505060402193](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505060403.png)

![image-20210505060423805](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505060424.png)

#### （2）双指针移动

![image-20210505061612837](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505061613.png)

```java
public double findMedianSortedArrays(int[] A, int[] B) {
    int m = A.length;
    int n = B.length;
    int len = m + n;
    int left = -1, right = -1;
    int aStart = 0, bStart = 0;
    //这里i是从0开始的，所以只要循环到len/2，不需要+1
    for (int i = 0; i <= len / 2; i++) {
        left = right;
        if (aStart < m && (bStart >= n || A[aStart] < B[bStart])) {
            right = A[aStart++];
        } else {
            right = B[bStart++];
        }
    }
    if ((len & 1) == 0)
        return (left + right) / 2.0;
    else
        return right;
}

作者：windliang
链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210505061707578](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505061708.png)

![image-20210505062546232](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505062547.png)

#### （3）第K小

![image-20210505163157316](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505163926.png)

![image-20210505163908380](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505163910.png)

![image-20210505164058267](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505164059.png)

![image-20210505164224899](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505164225.png)

###### 代码

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int n = nums1.length;
    int m = nums2.length;
    //这里就是取中位数，因为如果是偶数的话，是中间两个数，所以要取第k和第k+1小的数
    //如果是奇数的话，+1和+2结果都是一样的，所以求第k小的数是求了两次了
    int left = (n + m + 1) / 2;
    int right = (n + m + 2) / 2;
    //将偶数和奇数的情况合并，如果是奇数，会求两次同样的 k 。看最后是要除以2的，求中间两个数的平	//均值
    return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;  
}
    
    private int getKth(int[] nums1, int start1, int end1, int[] nums2, int start2, int end2, int k) {
        int len1 = end1 - start1 + 1;
        int len2 = end2 - start2 + 1;
        //让 len1 的长度小于 len2，这样就能保证如果有数组空了，一定是 len1 
        if (len1 > len2) return getKth(nums2, start2, end2, nums1, start1, end1, k);
        if (len1 == 0) return nums2[start2 + k - 1];

        if (k == 1) return Math.min(nums1[start1], nums2[start2]);
		//如果数组的长度比k/2小，那下标就直接定在长度的最后一位
        int i = start1 + Math.min(len1, k / 2) - 1;
        int j = start2 + Math.min(len2, k / 2) - 1;

        if (nums1[i] > nums2[j]) {
            return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
        }
        else {
            return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
        }
    }

作者：windliang
链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210505174658004](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505174658.png)

![image-20210505175452026](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505175453.png)

#### （4）根据中位数规律

![image-20210505230302001](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505230302.png)

![image-20210505230822882](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505230823.png)

![image-20210505231512787](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505231514.png)

![image-20210505232019937](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505232020.png)

![image-20210505232120271](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505232121.png)

![image-20210505232510741](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505232511.png)

![image-20210505232545776](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505232546.png)

###### 代码

```java
class Solution {
    public double findMedianSortedArrays(int[] A, int[] B) {
        int m = A.length;
        int n = B.length;
        if (m > n) { 
            return findMedianSortedArrays(B,A); // 保证 m <= n
        }
        //先确定i的位置，自然就能根据关系式得到j的位置了
        int iMin = 0, iMax = m;
        while (iMin <= iMax) {
            //一开始都先切在中间的位置，切的位置其实是二分的边界，这样也是通过二分查找位置不断地			//缩小范围，最终定位到i的准确位置，找到中位数的位置
            int i = (iMin + iMax) / 2;
            int j = (m + n + 1) / 2 - i;
            if (j != 0 && i != m && B[j-1] > A[i]){ // i 需要增大
                iMin = i + 1; 
            }
            else if (i != 0 && j != n && A[i-1] > B[j]) { // i 需要减小
                iMax = i - 1; 
            }
            //如果上面的条件满足了，说明已经找到i，j了，就是已经找到了中位数了，就是确定中位数的			//两个值，可以看图，要加上极端边界，这个时候左右两边的元素也是一样的
            else { // 达到要求，并且将边界条件列出来单独考虑
                int maxLeft = 0;
                if (i == 0) { maxLeft = B[j-1]; }
                else if (j == 0) { maxLeft = A[i-1]; }
                else { maxLeft = Math.max(A[i-1], B[j-1]); }
                if ( (m + n) % 2 == 1 ) { return maxLeft; } // 奇数的话不需要考虑右半部分

                int minRight = 0;
                if (i == m) { minRight = B[j]; }
                else if (j == n) { minRight = A[i]; }
                else { minRight = Math.min(B[j], A[i]); }

                return (maxLeft + minRight) / 2.0; //如果是偶数的话返回结果
            }
        }
        return 0.0;
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210505235735524](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505235741.png)

![image-20210505235806111](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210505235807.png)

## 186、[下一个排列](https://leetcode-cn.com/problems/next-permutation/)

![image-20210506012635605](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506012636.png)

### 思路

#### （1）大数小数交换

![image-20210506014853585](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506014854.png)

![image-20210506015227316](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506015228.png)

![image-20210506015423489](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506015424.png)

![image-20210506015532698](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506015533.png)

![image-20210506015605123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506015606.png)

![fig1](https://assets.leetcode-cn.com/solution-static/31/31.gif)

###### 代码

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = 0;
        if(nums == null || (n = nums.length) == 0)  return;
        // 从后往前查找第一次出现 nums[i] < nums[i+1] 的位置
        int i = n-2;
        //这里记得条件有个=，相等的话，也不要，一定要是升序的，才终止循环，找到i
        for(; i>=0 && nums[i] >= nums[i+1]; i--)    ;
        // if i == -1 nums 则整体逆序
        if(i >= 0){
            // 此时 nums[i+1, n-1] 降序, 查找其中比 大于nums[i] 的 最小的值，可以直接 从后往前 逆向找
            // 因为有序，是因为这段肯定是逆序的， 可以练习一下二分查找
            int j = binarySearch(nums, i+1, n-1, nums[i]);
            // 交换 i j
            swap(nums, i, j);
        }
        // 此时 nums[i+1, n-1] 仍然降序，将其改为升序，只需要反转即可。
        reverse(nums, i+1, n-1);
    }

    // nums[left, right] 逆序，查找其中 > target 的 最大下标
    private int binarySearch(int[] nums, int left, int right, int target){
        while(left <= right){
            //这里不要写成》》》2了
            int mid = (left + right) >>> 1;
            if(nums[mid] > target){
                left = mid + 1; // 尝试再缩小区间
            }else{
                //因为是逆序的，而跳出循环的时候，right肯定在left的左边，肯定是大的那个数
                //而且是最接近target的，并且大于它的那个数
                right = mid - 1;
            }
        }
        return right;
    }

    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }

    private void reverse(int[] nums, int i, int j){
        while(i < j){
            swap(nums, i++, j--);
        }
    }
}
```

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int i = nums.length - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i >= 0) {
            int j = nums.length - 1;
            while (j >= 0 && nums[i] >= nums[j]) {
                j--;
            }
            swap(nums, i, j);
        }
        reverse(nums, i + 1);
    }

    public void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    public void reverse(int[] nums, int start) {
        int left = start, right = nums.length - 1;
        while (left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/next-permutation/solution/xia-yi-ge-pai-lie-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210506023702611](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506023703.png)

![image-20210506024058327](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506024059.png)

## 187、[旋转图像](https://leetcode-cn.com/problems/rotate-image/)

![image-20210506034832394](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506034833.png)

![image-20210506034850026](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210506034850.png)

### 思路

#### （1）原地旋转

![image-20210509004332141](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509004335.png)

![image-20210509004647614](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509004648.png)



![image-20210509004724008](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509004725.png)

![image-20210509005104182](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509005104.png)

![image-20210508051020622](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210508051022.png)

###### 代码

```java
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int len = matrix.size();
        //旋转前一半，后一半也跟着旋转了 (matrix[len - i -1])
        for (int i = 0; i < len / 2; i++)
        {
            //每个方框的最后一列不用旋转，这样会重复
            for (int j = i; j < len - 1 - i; j++)
            {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[len - j - 1][i];
                matrix[len - j - 1][i] = matrix[len - i - 1][len - j - 1];
                matrix[len - i - 1][len - j - 1] = matrix[j][len - i - 1];
                matrix[j][len - i - 1] = temp;
            }
        }
    }
};
```

![image-20210508051823595](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210508051824.png)

```java
    public void rotate(int[][] matrix) {
        int length = matrix.length;
        //因为是对称的，只需要计算循环前半行即可
        for (int i = 0; i < length / 2; i++)
            for (int j = i; j < length - i - 1; j++) {
                int temp = matrix[i][j];
                int m = length - j - 1;
                int n = length - i - 1;
                matrix[i][j] = matrix[m][i];
                matrix[m][i] = matrix[n][m];
                matrix[n][m] = matrix[j][n];
                matrix[j][n] = temp;
            }
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/rotate-image/solution/shu-ju-jie-gou-he-suan-fa-liang-chong-sh-k3dy/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210509005143850](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509005144.png)

![image-20210509005955389](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509005956.png)

#### （2）翻转代替旋转

![image-20210509010308045](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509010308.png)

![image-20210509011010403](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509011011.png)

###### 代码

​	

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 水平翻转
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < n; ++j) {
                int temp = matrix[i][j];
                //注意这里是i的索引变了，上下交换了
                matrix[i][j] = matrix[n - i - 1][j];
                matrix[n - i - 1][j] = temp;
            }
        }
        // 主对角线翻转
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/rotate-image/solution/xuan-zhuan-tu-xiang-by-leetcode-solution-vu3m/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210509011117753](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509011118.png)

![image-20210509011810576](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509011811.png)

## 188、[跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

![image-20210509012245096](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509012246.png)

### 思路

#### （1）挨着跳

![image-20210509012826123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509012827.png)

```java
public static boolean canJump(int[] nums) {
        if (nums == null) {
            return false;
        }
        //前n-1个元素能够跳到的最远距离
        int k = 0;
        for (int i = 0; i <= k; i++) {
            //第i个元素能够跳到的最远距离
            int temp = i + nums[i];
            //更新最远距离
            k = Math.max(k, temp);
            //如果最远距离已经大于或等于最后一个元素的下标,则说明能跳过去,退出. 减少循环
            if (k >= nums.length - 1) {
                return true;
            }
        }
        //最远距离k不再改变,且没有到末尾元素
        return false;
    }
```

###### 复杂度分析

![image-20210509013220882](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509013221.png)

#### （2）递归（超时）

![image-20210509014003021](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509014003.png)

![image-20210509014046206](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509014047.png)

###### 代码

```java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums==null || nums.length==0) {
            return true;
        }
        return dfs(0,nums);
    }
    
    private boolean dfs(int index, int[] nums) {
        //递归的终止条件
        if(index>=nums.length-1) {
            return true;
        }
        //根据nums[index]表示要循环多少次，index是当前我们能到达的位置，
        //在这个基础上有 index+1，index+2.... index+i种跳跃选择
        for(int i=1;i<=nums[index];++i) {
            if(dfs(i+index,nums)) {
                return true;
            }
        }
        return false;
    }
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/jump-game/solution/chao-xiang-xi-tu-jie-di-gui-tan-xin-55-tiao-yue-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

会超时

![image-20210509014742997](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509014743.png)

#### （3）记忆化递归（超时）

![image-20210509014901233](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509014902.png)

```java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums==null || nums.length==0) {
            return true;
        }
        Map<Integer,Boolean> cache = new HashMap<Integer,Boolean>();
        return dfs(0,nums,cache);
    }
    
    private boolean dfs(int index, int[] nums, Map<Integer,Boolean> cache) {
        if(index>=nums.length-1) {
            return true;
        }
        if(cache.containsKey(index)) {
            return cache.get(index);
        }
        for(int i=1;i<=nums[index];++i) {
            if(dfs(i+index,nums,cache)) {
                cache.put((i+index),Boolean.TRUE);
                return true;
            }
        }
        cache.put(index,Boolean.FALSE);
        return false;
    }
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/jump-game/solution/chao-xiang-xi-tu-jie-di-gui-tan-xin-55-tiao-yue-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

还是超时



#### （4）贪心算法

![image-20210509022800477](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509022801.png)

![image-20210509022812557](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509022814.png)

![image-20210509022837105](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509022838.png)

![image-20210509022857114](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509022858.png)

###### 代码

```java
class Solution {
    public boolean canJump(int[] nums) {
        if(nums==null || nums.length==0) {
            return true;
        }
        int n = nums.length;
        int ans = 0;
        //遍历数组，尝试扩大最大可达范围
        for(int i=0;i<n;++i) {
            //当前的下标要小于之前的最大可达范围，说明当前的坐标可以到达，然后更新,在当前坐标比			//最远可到达的距离大的话，说明当前的i之前的最大可达范围，已经到不了了,其实可以break掉了
            if(ans>=i) {
                ans = Math.max(ans,i+nums[i]);
            }
        }
        if(ans>=n-1) {
            return true;
        }
        return false;
    }
}	

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/jump-game/solution/chao-xiang-xi-tu-jie-di-gui-tan-xin-55-tiao-yue-yo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210509023153049](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210509023153049.png)

#### （5）逆推

![image-20210509024625674](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509024626.png)

```java
public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0) {
            return false;
        }
        //pos表示需要到达的位置
        int pos = nums.length - 1;
        for (int i = nums.length - 2; i >= 0; i--) {
            if (nums[i] + i >= pos) {
                pos = i;
            }

        }
        return pos == 0;

    }

作者：reedfan
链接：https://leetcode-cn.com/problems/jump-game/solution/shun-zhao-tui-dao-zhao-tui-liang-chong-fang-shi-ji/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### （6）动态规划

![image-20210509025128255](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509025128.png)

```java
class Solution {
    public boolean canJump(int[] nums) {
        
        if (nums == null) {
            return false;
        }
        boolean[] dp = new boolean[nums.length];
        dp[0] = true;
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                // 如果之前的j节点可达，并且从此节点可以到跳到i
                if (dp[j] && nums[j] + j >= i) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[nums.length - 1];
    }
}

作者：yangtianrui95
链接：https://leetcode-cn.com/problems/jump-game/solution/dong-tai-gui-hua-yu-tan-xin-suan-fa-jie-jue-ci-wen/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 189、[合并区间](https://leetcode-cn.com/problems/merge-intervals/)

![image-20210509113945457](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509114017.png)

### 思路

#### （1）直接合并

![image-20210509120505180](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509120507.png)

![image-20210509120157736](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509120158.png)

![image-20210509120633150](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509120633.png)



![image-20210509122054979](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509122055.png)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        // 先按照区间起始位置排序
        Arrays.sort(intervals, (v1, v2) -> v1[0] - v2[0]);
        // 遍历区间
        int[][] res = new int[intervals.length][2];
        int idx = -1;
        for (int[] interval: intervals) {
            // 如果结果数组是空的，或者当前区间的起始位置 > 结果数组中最后区间的终止位置，
            // 则不合并，直接将当前区间加入结果数组。
            if (idx == -1 || interval[0] > res[idx][1]) {
                res[++idx] = interval;
            } else {
                // 反之将当前区间合并至结果数组的最后区间
                res[idx][1] = Math.max(res[idx][1], interval[1]);
            }
        }
        return Arrays.copyOf(res, idx + 1);
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/merge-intervals/solution/chi-jing-ran-yi-yan-miao-dong-by-sweetiee/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```JAVA
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.Stack;


public class Solution {

    public int[][] merge(int[][] intervals) {
        int len = intervals.length;
        if (len < 2) {
            return intervals;
        }

        // 按照起点排序
        Arrays.sort(intervals, Comparator.comparingInt(o -> o[0]));

        // 也可以使用 Stack，因为我们只关心结果集的最后一个区间
        List<int[]> res = new ArrayList<>();
        res.add(intervals[0]);

        for (int i = 1; i < len; i++) {
            int[] curInterval = intervals[i];

            // 每次新遍历到的列表与当前结果集中的最后一个区间的末尾端点进行比较
            int[] peek = res.get(res.size() - 1);

            if (curInterval[0] > peek[1]) {
                res.add(curInterval);
            } else {
                // 注意，这里应该取最大
                peek[1] = Math.max(curInterval[1], peek[1]);
            }
        }
        return res.toArray(new int[res.size()][]);
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[][] intervals = {{1, 3}, {2, 6}, {8, 10}, {15, 18}};
        int[][] res = solution.merge(intervals);
        for (int i = 0; i < res.length; i++) {
            System.out.println(Arrays.toString(res[i]));
        }
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/merge-intervals/solution/tan-xin-suan-fa-java-by-liweiwei1419-3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210509115742441](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509115743.png)

![image-20210509120804843](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509120805.png)

## 190、[不同路径](https://leetcode-cn.com/problems/unique-paths/)

![image-20210509160006109](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509160007.png)

![image-20210509160050620](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509160051.png)

### 思路

#### （1）动态规划

![image-20210509162803744](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509162804.png)

![image-20210509160415589](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509160416.png)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for (int i = 0; i < n; i++) dp[0][i] = 1;
        for (int i = 0; i < m; i++) dp[i][0] = 1;
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];  
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-by-powcai-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210509160742351](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509160743.png)

![image-20210509160817258](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509160818.png)

#### （2）优化1

> 令m为行、n为列 优化1：行列两层循环中的循环体cur[j] = pre[j] + cur[j-1] ，cur[j] 表示遍历到的从起点到第i行第j列的路径数，它等于当前第i行第j-1列即 cur[j-1]的值 加上 上一行第j列的值pre[j] 内层循环一次后即计算完了第i行各列的值，在计算下一行第i+1行之前执行pre = cur.clone(); 即第i行的值就是第i+1行的前一行，两层循环完以后最后要到达的终点的行的值存于pre数组中，所以取出 pre[n-1]即可

![image-20210509162713495](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509162714.png)

###### 代码

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] pre = new int[n];
        int[] cur = new int[n];
        Arrays.fill(pre, 1);
        Arrays.fill(cur,1);
        for (int i = 1; i < m;i++){
            for (int j = 1; j < n; j++){
                cur[j] = cur[j-1] + pre[j];
            }
            pre = cur.clone();
        }
        return pre[n-1]; 
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-by-powcai-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

空间复杂度 O(2n)

#### （3）优化2

![image-20210509162936943](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509162937.png)

> 优化2：相比优化1，少了pre数组，cur[j] += cur[j-1] 即 cur[j] = cur[j-1] + cur[j] 未赋值之前右边的cur[j] 始终表示当前行第i行的上一行第j列的值，赋值之后左边的cur[j]表示当前行第i行第j列的值，cur[j-1] 表示当前行第i行第j-1列的值(cur[j-1] 在计算cur[j]之前就已经计算了，所以表示的是当前行而不是上一行 )， 思路跟优化1是一样的，除了少用了一个数组
>
> 

###### 代码

![image-20210509165617314](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509165618.png)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[] cur = new int[n];
        Arrays.fill(cur,1);
        //i这里是逐层遍历
        for (int i = 1; i < m;i++){
            //j这里是针对当前的i层，逐列填充过去
            for (int j = 1; j < n; j++){
                //这里很重要要理解下，在执行循环体cur[j] += cur[j-1]，cur[j]是已经有值的
                //是上个i循环在j位置的值，就是代表上一层的路径数，而cur[j-1]起始就是本层j
                //的上一次循环，已经求解更新过了，就是当前层的当前位置的前一个位置的路径数
                //两个相加即可
                cur[j] += cur[j-1] ;
            }
        }
        return cur[n-1];
    }
}

作者：powcai
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-by-powcai-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210509165227919](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509165228.png)

#### （4）递归的优化

###### 优化之前

```java
public int uniquePaths(int m, int n) {
    return uniquePathsHelper(1, 1, m, n);
}

//第i行第j列到第m行第n列共有多少种路径
public int uniquePathsHelper(int i, int j, int m, int n) {
    //边界条件的判断
    if (i > m || j > n)
        return 0;
    if ((i == m && j == n))
        return 1;
    //从右边走有多少条路径
    int right = uniquePathsHelper(i + 1, j, m, n);
    //从下边走有多少条路径
    int down = uniquePathsHelper(i, j + 1, m, n);
    //返回总的路径
    return right + down;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-di-gui-gong-shi-deng-3z-9mp1/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210509165847829](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509165848.png)

###### 备忘录模式优化

```java
public int uniquePaths(int m, int n) {
    return uniquePathsHelper(1, 1, m, n, new HashMap<>());
}

public int uniquePathsHelper(int i, int j, int m, int n, Map<String, Integer> map) {
    if (i > m || j > n)
        return 0;
    //递归的终止条件不变还是要写
    if ((i == m && j == n))
        return 1;
    String key = i + "*" + j;
    //如果已经计算过路径数的点，直接返回
    if (map.containsKey(key))
        return map.get(key);
    int right = uniquePathsHelper(i + 1, j, m, n, map);
    int down = uniquePathsHelper(i, j + 1, m, n, map);
    int totla = right + down;
    map.put(key, totla);
    //这里存结果到缓存中，表示从当前点出发的路径数，然后还是要返回这个路劲数个上一层
    return totla;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-di-gui-gong-shi-deng-3z-9mp1/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析



![image-20210509170653515](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509170654.png)

![image-20210509171317614](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509171318.png)

#### （5）公式

![image-20210509170738927](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509170739.png)

![image-20210509171050038](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509171050.png)

```java
public int uniquePaths(int m, int n) {
    int N = n + m - 2;
    double res = 1;
    for (int i = 1; i < m; i++)
        res = res * (N - (m - 1) + i) / i;
    return (int) res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-di-gui-gong-shi-deng-3z-9mp1/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 191、[最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

![image-20210509171601761](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509171602.png)

### 思路

#### （1）动态规划

![image-20210509180647147](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509180648.png)

![img](https://pic.leetcode-cn.com/c32cf8caeabc08a2a759bb0eff310cfa3a424617e3b2f342d18a4ce6e1b450c8-Picture1.png)

![img](https://pic.leetcode-cn.com/9d45c8b3fe3111048b1e24a21ae5a7bc16b8c431e62ada811136152d43e2d27e-Picture2.png)

![img](https://pic.leetcode-cn.com/a2d4f3fdafdbc564d5b8bf63385601feb7269f72bbb716eb031120d9bea7921f-Picture3.png)

![img](https://pic.leetcode-cn.com/b93a6e07cdc774a051bd7abef8fe9c99d582a07b89c1f7e43edfc9921df2f773-Picture4.png)

![img](https://pic.leetcode-cn.com/a833e2fe055ce5f996852681821517c3aa6da792f2c24b67f7840db90f5f805c-Picture5.png)

![img](https://pic.leetcode-cn.com/f7b184924ac3b0e01f779321754bc98b5322351ee2791d04f3a15d83e4ce2fff-Picture6.png)

![img](https://pic.leetcode-cn.com/dc0811c743893c3d18ad3f12e15f310506748429ab1df814cddce43069ab607e-Picture9.png)

![img](https://pic.leetcode-cn.com/488267d5cadfbd697d92311a79ad6c64aec64e018ef094cf2df71cac491d69ca-Picture10.png)



###### 代码

```java
class Solution {
    public int minPathSum(int[][] grid) {
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[0].length; j++) {
                if(i == 0 && j == 0) continue;
                else if(i == 0)  grid[i][j] = grid[i][j - 1] + grid[i][j];
                else if(j == 0)  grid[i][j] = grid[i - 1][j] + grid[i][j];
                else grid[i][j] = Math.min(grid[i - 1][j], grid[i][j - 1]) + grid[i][j];
            }
        }
        return grid[grid.length - 1][grid[0].length - 1];
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/minimum-path-sum/solution/zui-xiao-lu-jing-he-dong-tai-gui-hua-gui-fan-liu-c/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210509180809410](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509180810.png)

#### （2）二维优化一维

```java
    public int minPathSum(int[][] grid) {
		int len = grid[0].length;
		int[] dp = new int[len];
		dp[0] = grid[0][0];
		for (int i = 1; i < len; i++) 
            //第一行的要先求，注意这里一定是+dp[i-1]不是加自身dp[i]
			dp[i]=dp[i-1]+grid[0][i];
		for (int i = 1; i < grid.length; i++) {
            //这里一定要直接求第一位的数，就是上层的第一位的数+当前层第一位的数
			dp[0] = dp[0] + grid[i][0];
			for (int j = 1; j < len; j++) 
				dp[j] = Math.min(dp[j-1]+grid[i][j], dp[j]+grid[i][j]);
		}
		return dp[len-1];
    }
```

###### 复杂度分析

![image-20210509181928864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509181929.png)

## 192、[N 皇后](https://leetcode-cn.com/problems/n-queens/)

![image-20210509182201002](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509182201.png)

### 思路

#### （1）回溯算法

![img](https://pic.leetcode-cn.com/1597914538-JMqrTI-%E5%B9%BB%E7%81%AF%E7%89%871.png)

![img](https://pic.leetcode-cn.com/1597914538-ZjQXmZ-%E5%B9%BB%E7%81%AF%E7%89%872.png)

![img](https://pic.leetcode-cn.com/1597914538-tmYgmS-%E5%B9%BB%E7%81%AF%E7%89%873.png)

![image-20210509235851458](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210509235853.png)

![img](https://pic.leetcode-cn.com/1597914538-OtaRLU-%E5%B9%BB%E7%81%AF%E7%89%876.png)

![img](https://pic.leetcode-cn.com/1597914538-rucjiO-%E5%B9%BB%E7%81%AF%E7%89%877.png)

![image-20210510000013724](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510000014.png)

![img](https://pic.leetcode-cn.com/1597914538-mxIubY-%E5%B9%BB%E7%81%AF%E7%89%8716.png)

![img](https://pic.leetcode-cn.com/1597914538-ZnPNRW-%E5%B9%BB%E7%81%AF%E7%89%8717.png)

![img](https://pic.leetcode-cn.com/1597914538-voxIfs-%E5%B9%BB%E7%81%AF%E7%89%8718.png)

![img](https://pic.leetcode-cn.com/1597914538-xyeSxZ-%E5%B9%BB%E7%81%AF%E7%89%8719.png)

![img](https://pic.leetcode-cn.com/1597914538-Gcnqba-%E5%B9%BB%E7%81%AF%E7%89%8720.png)

![img](https://pic.leetcode-cn.com/1597914538-VYBADD-%E5%B9%BB%E7%81%AF%E7%89%8721.png)

![img](https://pic.leetcode-cn.com/1597914538-ORsUfU-%E5%B9%BB%E7%81%AF%E7%89%8730.png)

###### 理解树形结构

![image-20210510000153795](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510000154.png)

![image-20210510000446353](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510000447.png)

![image-20210510013915206](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510013915.png)

![image-20210510013925561](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510013926.png)

![image-20210510000458507](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510000458.png)

###### 代码

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    private int n;
    /**
     * 记录某一列是否放置了皇后
     */
    private boolean[] col;
    /**
     * 记录主对角线上的单元格是否放置了皇后
     */
    private boolean[] main;
    /**
     * 记录了副对角线上的单元格是否放置了皇后
     */
    private boolean[] sub;
    private List<List<String>> res;

    public List<List<String>> solveNQueens(int n) {
        res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        // 设置成员变量，减少参数传递，具体作为方法参数还是作为成员变量，请参考团队开发规范
        this.n = n;
        this.col = new boolean[n];
        this.main = new boolean[2 * n - 1];
        this.sub = new boolean[2 * n - 1];
        Deque<Integer> path = new ArrayDeque<>();
        dfs(0, path);
        return res;
    }

    private void dfs(int row, Deque<Integer> path) {
        if (row == n) {
            // 深度优先遍历到下标为 n，表示 [0.. n - 1] 已经填完，得到了一个结果
            List<String> board = convert2board(path);
            res.add(board);
            return;
        }

        // 针对下标为 row 的每一列，尝试是否可以放置
        for (int j = 0; j < n; j++) {
            //这里判断是不是在同列，同对角线，其中row-j相等的，表示是在同一对角线上，自然对应的是visited数组中的同一个位置了，但是结果可能为负数，所以要+(n-1)平移不要越界了，row-j负数的最小值
           //row为0，j为n-1的时候，这个值为-n+1，所以加上上面那个值就刚好从0开始了
            //第二个row+j是副对角线，如果横纵坐标相加是一样的，那肯定在同一个对角线上，对应visited数组肯定也是一个位置的，判断这个对角线上是不是已经放了皇后了
            if (!col[j] && !main[row - j + n - 1] && !sub[row + j]) {
                path.addLast(j);
                col[j] = true;
                main[row - j + n - 1] = true;
                sub[row + j] = true;


                dfs(row + 1, path);
                sub[row + j] = false;
                main[row - j + n - 1] = false;
                col[j] = false;
                path.removeLast();
            }
        }
    }

     public List<String> buildStr(Deque<Integer> path){
        List<String> part=new ArrayList<>();
        for(Integer num:path){
            char [] ch=new char[n];
            Arrays.fill(ch,'.');
            ch[num]='Q';
            part.add(new String(ch));
        }
        return part;

    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/n-queens/solution/gen-ju-di-46-ti-quan-pai-lie-de-hui-su-suan-fa-si-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210510013557432](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510013557.png)

![image-20210510013957747](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510013958.png)

###### 代码2：会用哈希表

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Solution {

    private Set<Integer> col;
    private Set<Integer> main;
    private Set<Integer> sub;
    private int n;
    private List<List<String>> res;

    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        col = new HashSet<>();
        main = new HashSet<>();
        sub = new HashSet<>();

        Deque<Integer> path = new ArrayDeque<>();
        dfs(0, path);
        return res;
    }

    private void dfs(int row, Deque<Integer> path) {
        if (row == n) {
            List<String> board = convert2board(path);
            res.add(board);
            return;
        }

        // 针对每一列，尝试是否可以放置
        for (int i = 0; i < n; i++) {
            if (!col.contains(i) && !main.contains(row - i) && !sub.contains(row + i)) {
                path.addLast(i);
                col.add(i);
                main.add(row - i);
                sub.add(row + i);

                dfs(row + 1, path);

                sub.remove(row + i);
                main.remove(row - i);
                col.remove(i);
                path.removeLast();
            }
        }
    }

    private List<String> convert2board(Deque<Integer> path) {
        List<String> board = new ArrayList<>();
        for (Integer num : path) {
            StringBuilder row = new StringBuilder();
            row.append(".".repeat(Math.max(0, n)));
            row.replace(num, num + 1, "Q");
            board.add(row.toString());
        }
        return board;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/n-queens/solution/gen-ju-di-46-ti-quan-pai-lie-de-hui-su-suan-fa-si-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210510013717940](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210510013718.png)

#### （2）位运算





###### 代码

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        // 这个数组用于记录每行中皇后所在的位置
        int[] queens=new int[n];
        // 官方在这里用-1填充queens数组，但是和下面的状态重置一样，没有必要
        // Arrays.fill(queens,-1);
        List<List<String>> results=new ArrayList<>();
        solveNQueens(results,queens,n,0,0,0,0);
        return results;
    }
    /**
    * int n：总行数
    * int row：当前行数
    * int columns：不可选的列
    * int diagonals1：不可选的左斜边
    * int diagonals2：不可选的右斜边
    */
    public void solveNQueens(List<List<String>> results,int[] queens,int n,int row,int columns,int diagonals1,int diagonals2){
        // 如果能到达这一步，说明搜索已经到底了，我们已经记录下了一个可行的方案
        if(row==n){
            // 直接生成一个结果，并放入结果集中
            results.add(generateString(queens));
            // 方法终止
            return;
        }
        // 1<<n-1 是为了转化一个长度为n的，每位上都是1的二进制数，用于定位可以放置皇后的位置
        // 这里用于定位所有可选的位置，这里有一步取反，千万不要忽视了！
        // 上面我们用 1 表示不可选的位置，但是这里我们取反后，用1表示可选的位置
        int availableLocations=((1<<n)-1)&(~(columns|diagonals1|diagonals2));
        // 我们通过下面的操作来保持之前所有的行对下一行的影响
        // 左斜边因为下降了一行需要左移一位
        diagonals1<<=1;
        // 右斜边因为下降了一行需要右移一位
        diagonals2>>=1;
        // 开始检查每个可选的位置
        while(availableLocations!=0){
            // 定位最后一个1的位置，这个操作可以自己手写验证一下（不要忘了把负数转成补码）
            // 这个定位的意思是，生成的这个二进制数只有最后一个1还为1，其他位都变成了0
            int position=availableLocations&(-1*availableLocations);
            // 这个方法是统计一个二进制数中所有的“1”的个数
            int columnNum=Integer.bitCount(position-1);
            // 将这个位置添加到记录数组中
            queens[row]=columnNum;
            // 将这一位从可选取的位中移除
            // 减1把最后一个1拆成后面的多个1，再经过一次与操作把这些多出来的1全部清除 
            availableLocations=availableLocations&(availableLocations-1);
            // 沿着这个位置向下搜索，可选行和可选列的直接在参数上变化即可，这样就不需要手动重置状态了
            solveNQueens(results,queens,n,row+1,columns|position,diagonals1|position<<1,diagonals2|position>>1);
            // 官方在这里曾经重置过数组queens的状态，但实际上没这个必要，每次循环上一次的结果都会被覆盖
            // queens[row]=-1;
        }
    }
    // 生成字符串
    public List<String> generateString(int[] queens){
        List<String> result=new ArrayList<>();
        for(int i:queens){
            char[] chars=new char[queens.length];
            Arrays.fill(chars,'.');
            chars[i]='Q';
            result.add(String.valueOf(chars));
        }
        return result;
    }
}
```



## 193、[颜色分类](https://leetcode-cn.com/problems/sort-colors/)

![image-20210511000526164](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511000528.png)

### 思路

#### （1）双指针

![image-20210511020132681](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511020134.png)

###### 代码

```java
import java.util.Arrays;


public class Solution {

    public void sortColors(int[] nums) {
        int len = nums.length;
        if (len < 2) {
            return;
        }

        // all in [0, zero) = 0
        // all in [zero, i) = 1
        // all in [two, len - 1] = 2
        
        // 循环终止条件是 i == two，那么循环可以继续的条件是 i < two
        // 为了保证初始化的时候 [0, zero) 为空，设置 zero = 0，
        // 所以下面遍历到 0 的时候，先交换，再加
        int zero = 0;

        // 为了保证初始化的时候 [two, len - 1] 为空，设置 two = len
        // 所以下面遍历到 2 的时候，先减，再交换
        int two = len;
        int i = 0;
        // 当 i == two 上面的三个子区间正好覆盖了全部数组
        // 因此，循环可以继续的条件是 i < two
        while (i < two) {
            if (nums[i] == 0) {
                swap(nums, i, zero);
                //zero指向的位置都是不为0的位置，便于跟0的位置交换，所以是开区间
                zero++;
                i++;
            } else if (nums[i] == 1) {
               //i指向的位置肯定不是1，所以也是开区间
                i++;
            } else {
                //two指向的位置肯定是2，所以要先减再去交换
                two--;
                swap(nums, i, two);
            }
        }
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/sort-colors/solution/kuai-su-pai-xu-partition-guo-cheng-she-ji-xun-huan/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 代码2

```java
public class Solution {

    public void sortColors(int[] nums) {
        int len = nums.length;
        if (len < 2) {
            return;
        }
        // all in [0, zero] = 0
        // all in (zero, i) = 1
        // all in (two, len - 1] = 2

        // 为了保证初始化的时候 [0, zero] 为空，设置 zero = -1，
        // 所以下面遍历到 0 的时候，先加，再交换
        int zero = -1;

        // 为了保证初始化的时候 (two, len - 1] 为空，设置 two = len - 1
        // 所以下面遍历到 2 的时候，先交换，再减
        int two = len - 1;
        int i = 0;
        // 当 i == two 的时候，还有一个元素还没有看，
        // 因此，循环可以继续的条件是 i <= two
        while (i <= two) {
            if (nums[i] == 0) {
                zero++;
                swap(nums, i, zero);
                i++;
            } else if (nums[i] == 1) {
                i++;
            } else {
                swap(nums, i, two);
                two--;
            }
        }
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/sort-colors/solution/kuai-su-pai-xu-partition-guo-cheng-she-ji-xun-huan/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 代码3

![img](https://assets.leetcode-cn.com/solution-static/75/2_14.png)

![image-20210511021151835](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511021152.png)

![img](https://assets.leetcode-cn.com/solution-static/75/2_17.png)



![img](https://assets.leetcode-cn.com/solution-static/75/2_18.png)



```java
class Solution {
    public void sortColors(int[] nums) {
        int n = nums.length;
        int p0 = 0, p1 = 0;
        for (int i = 0; i < n; ++i) {
            //一定要先判断是不是等于1，这样才有下面的判断如果p0<p1
            if (nums[i] == 1) {
                int temp = nums[i];
                nums[i] = nums[p1];
                nums[p1] = temp;
                ++p1;
            } else if (nums[i] == 0) {
                int temp = nums[i];
                nums[i] = nums[p0];
                nums[p0] = temp;
                if (p0 < p1) {
                    temp = nums[i];
                    nums[i] = nums[p1];
                    nums[p1] = temp;
                }
                ++p0;
                ++p1;
            }
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/sort-colors/solution/yan-se-fen-lei-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 代码4

![image-20210511023508000](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511023508.png)

```java
class Solution {
    public void sortColors(int[] nums) {
        int n = nums.length;
        int p0 = 0, p2 = n - 1;
        for (int i = 0; i <= p2; ++i) {
            //因为交换过来的当前值可能还是2，所以得继续放到p2位置
            while (i <= p2 && nums[i] == 2) {
                int temp = nums[i];
                nums[i] = nums[p2];
                nums[p2] = temp;
                --p2;
            }
            //这里就没事了，如果当前是0的话，而p0指着肯定是0的位置，所以就是自身交换
            if (nums[i] == 0) {
                int temp = nums[i];
                nums[i] = nums[p0];
                nums[p0] = temp;
                ++p0;
            }
        }
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/sort-colors/solution/yan-se-fen-lei-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![img](https://assets.leetcode-cn.com/solution-static/75/3_4.png)

![img](https://assets.leetcode-cn.com/solution-static/75/3_6.png)

###### 复杂度分析

![image-20210511020201369](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511020202.png)

![image-20210511020404515](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511020405.png)

## 194、[单词搜索](https://leetcode-cn.com/problems/word-search/)

![image-20210511024515543](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511024516.png)

![image-20210511024535324](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511024536.png)

### 思路

#### （1）回溯算法

![img](https://pic.leetcode-cn.com/d504319785cb9ee880004a94f1318954f66ea5b29459db44b81ebc048129c98a-79-1.png)

![img](https://pic.leetcode-cn.com/ce56eaaf43b587ad6e02bd3951b27291cf27d159106a984ab73659c511faa87d-79-2.png)

![img](https://pic.leetcode-cn.com/631f0247763caba9fa164888f80459e9985826ed1393f3032363f999456b3025-79-3.png)

![img](https://pic.leetcode-cn.com/9a3a3cc259208cb391ee7065c5be446091bab7b29094a3d5122918569835a6fc-79-4.png)

![img](https://pic.leetcode-cn.com/b74270d9ddd333a7d92c8cb791044ef8b0e272db568a51eb7d547cdc8b6999f8-79-5.png)

![img](https://pic.leetcode-cn.com/06fe072d677aeb8611f3169ed294d1af7a4bc8693c0c5c6898c8bb24eee92c91-79-6.png)

![img](https://pic.leetcode-cn.com/4ad2839afb72036fec361e1afff1669650279b64cf4f2d791400818f49f71f7a-79-7.png)

![img](https://pic.leetcode-cn.com/6b8b17fd56c4e51e9af162ae92e31b2b5245411c47f25772a7946bb938bcc993-79-8.png)

![img](https://pic.leetcode-cn.com/ffb4b6ec9430eb17cfd6d56af0cd6b941bd4f444198c9e133884ec6fd5e7b290-79-10.png)

![img](https://pic.leetcode-cn.com/adf76299376889d03ddc7d532213c7e5381ac14f3d01e7ee80f2945e265d23a2-79-11.png)

![img](https://pic.leetcode-cn.com/bc375f253b7e7453e46ee1282779e1067808766454fc8a4cbd9d570f7c9454a3-79-12.png)

![img](https://pic.leetcode-cn.com/80a6087835851324b091ce1e6c9dea8760cde57f4f2b35cfdac4a001bb42429b-79-13.png)

###### 代码

```java
public class Solution {
	//这个也太他妈多的成员变量了吧 有他妈的必要吗 脑子都看花了，不要用这个写法，直到即可
    private static final int[][] DIRECTIONS = {{-1, 0}, {0, -1}, {0, 1}, {1, 0}};
    private int rows;
    private int cols;
    private int len;
    private boolean[][] visited;
    private char[] charArray;
    private char[][] board;

    public boolean exist(char[][] board, String word) {
        rows = board.length;
        if (rows == 0) {
            return false;
        }
        cols = board[0].length;
        visited = new boolean[rows][cols];

        this.len = word.length();
        this.charArray = word.toCharArray();
        this.board = board;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean dfs(int x, int y, int begin) {
        if (begin == len - 1) {
            return board[x][y] == charArray[begin];
        }
        if (board[x][y] == charArray[begin]) {
            visited[x][y] = true;
            //这里是一个位置递归查找上下左右
            for (int[] direction : DIRECTIONS) {
                int newX = x + direction[0];
                int newY = y + direction[1];
                if (inArea(newX, newY) && !visited[newX][newY]) {
                    if (dfs(newX, newY, begin + 1)) {
                        return true;
                    }
                }
            }
            //这里是如果一个点先满足跟目标单词的一个词一样，先标记了这个单词已经找过了，再上下左			//右的遍历，发现四周都没有单词的下个字符，就要回溯到上个位置，但是要把当前位置置为未访		//问过，因为别的路线是可以再次访问这个位置的
            visited[x][y] = false;
        }
        return false;
    }

    private boolean inArea(int x, int y) {
        return x >= 0 && x < rows && y >= 0 && y < cols;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/word-search/solution/zai-er-wei-ping-mian-shang-shi-yong-hui-su-fa-pyth/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 代码2

```java
public boolean exist(char[][] board, String word) {
    char[] words = word.toCharArray();
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            //从[i,j]这个坐标开始查找
            if (dfs(board, words, i, j, 0))
                return true;
        }
    }
    return false;
}

boolean dfs(char[][] board, char[] word, int i, int j, int index) {
    //边界的判断，如果越界直接返回false。index表示的是查找到字符串word的第几个字符，
    //如果这个字符不等于board[i][j]，说明验证这个坐标路径是走不通的，直接返回false
    if (i >= board.length || i < 0 || j >= board[0].length || j < 0 || board[i][j] != word[index])
        return false;
    //如果word的每个字符都查找完了，直接返回true
    if (index == word.length - 1)
        return true;
    //把当前坐标的值保存下来，为了在最后复原
    char tmp = board[i][j];
    //然后修改当前坐标的值，修改的坐标的值，不是单词数组的值
    board[i][j] = '.';
    //走递归，沿着当前坐标的上下左右4个方向查找
    boolean res = dfs(board, word, i + 1, j, index + 1) || dfs(board, word, i - 1, j, index + 1) ||
            dfs(board, word, i, j + 1, index + 1) || dfs(board, word, i, j - 1, index + 1);
    //递归之后再把当前的坐标复原，修改坐标的值，其实是因为你递归当前位置隔壁的坐标，那当前的坐标
    //肯定会作为隔壁坐标的上下左右来遍历到了，所以这里就是为了标识已被遍历了，还原坐标，其实就是
    //为了如果四个方向的字符都不符合要去的情况，那要回溯了，回到上个位置，那当前位置相当于在别的
    //路线的时候是还没遍历的
    board[i][j] = tmp;
    return res;
}

作者：sdwwld
链接：https://leetcode-cn.com/problems/word-search/solution/hui-su-suan-fa-qiu-jie-by-sdwwld-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210511050447781](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511050449.png)

###### 复杂度分析

![image-20210511050551098](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511050551.png)

## 195、[环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

![image-20210511051110320](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511051111.png)

![image-20210511051123504](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511051124.png)

### 思路

#### （1）双指针法

![image-20210511053506064](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511053506.png)

![img](https://pic.leetcode-cn.com/a4788076d4f3ad247c2023f92bb1585d05c5132ece7ed1205e2e171e25648adc-Picture1.png)

![img](https://pic.leetcode-cn.com/4ccc10d8af901acf43f4db0e5cd0e3c537aeb2346f57ad66c92cb9cbba0f1f73-Picture2.png)

![img](https://pic.leetcode-cn.com/5bfd893f81962daed27dd9fc3c96e426b168f4e940e5ab7541c323ee416548ec-Picture3.png)



![img](https://pic.leetcode-cn.com/387bfbbe71b3f1d462f72472b8168b894b7c41907e8a66bb770cd7a7ad04d48d-Picture4.png)

![img](https://pic.leetcode-cn.com/54d3a446f6acf92de2e51e639fb4f05abffa468334a778bd74c63f990cd73276-Picture5.png)

![img](https://pic.leetcode-cn.com/9a319387f7fe8d3c3acb9d6bc0bc9f7471ccff6699115db724a99d2acb7b68ca-Picture6.png)

![img](https://pic.leetcode-cn.com/f3977a8e28b45952e01334c1c86d70e3e822c913f81318108052aea81e365788-Picture7.png)

![img](https://pic.leetcode-cn.com/114969493875dcdca1d1bea8fb997643975d25b4ddb185dd071a185ed435cccd-Picture8.png)

![img](https://pic.leetcode-cn.com/c7ab2f7023d5f8c7fcae71280b56c1ec6acf65f634ef82d61713fcff1ea2ee75-Picture9.png)

![img](https://pic.leetcode-cn.com/af490a825982d42be6baf7e87a3e1cf181420bb9f46aa0ccbb190719c8b4dd92-Picture10.png)

![img](https://pic.leetcode-cn.com/f31767986757b751bfec07f824714044611b4a54bf8e794b2f4684a175dde044-Picture11.png)



###### 代码

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        //如果fast为null，或者它的下个next为null，那肯定没有环，返回null，fast不为null，那
        //slow肯定不会为null了，因为fast跑在前面探路的
        while (fast != null) {
            slow = slow.next;
            if (fast.next != null) {
                fast = fast.next.next;
            } else {
                return null;
            }
            if (fast == slow) {
                ListNode ptr = head;
                while (ptr != slow) {
                    ptr = ptr.next;
                    slow = slow.next;
                }
                return ptr;
            }
        }
        return null;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/huan-xing-lian-biao-ii-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210511053629086](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511053629.png)

![image-20210511053954896](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511053955.png)

#### （2）哈希表

一个非常直观的思路是：我们遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环。借助哈希表可以很方便地实现。

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode pos = head;
        Set<ListNode> visited = new HashSet<ListNode>();
        while (pos != null) {
            if (visited.contains(pos)) {
                return pos;
            } else {
                visited.add(pos);
            }
            pos = pos.next;
        }
        return null;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/huan-xing-lian-biao-ii-by-leetcode-solution/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210511054928358](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511054928.png)

## 196、[排序链表](https://leetcode-cn.com/problems/sort-list/)

![image-20210511055020629](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511055021.png)

![image-20210511055034509](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210511055035.png)

### 思路

#### （1）自顶向下归并排序

![image-20210512045303057](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512045304.png)

![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512045304.png)

###### 关键技巧

###### 1.1 **通过快慢指针找到链表中点**

![image-20210512050956167](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512050957.png)

![image-20210512051141874](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512051142.png)

###### 1.2 **断链操作**









###### 递归代码

```java
class Solution {
    public ListNode sortList(ListNode head) {
        //递归终止条件是分割到单个结点
        if (head == null || head.next == null)
            return head;
        //这里fast比slow前面一个，是为了如果是偶数结点的话，slow指针指向中间的前面一个结点
        ListNode fast = head.next, slow = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        //这个是另一半链表的头结点
        ListNode tmp = slow.next;
        //这里是断开链表
        slow.next = null;
        ListNode left = sortList(head);
        ListNode right = sortList(tmp);
        ListNode h = new ListNode(0);
        //res是一个哑节点，h是拿来移动的
        ListNode res = h;
        //交替移动指针来进行比较，两两链表合并
        while (left != null && right != null) {
            if (left.val < right.val) {
                h.next = left;
                left = left.next;
            } else {
                h.next = right;
                right = right.next;
            }
            h = h.next;
        }
        //这里是把剩下的一个链表直接合并进来
        h.next = left != null ? left : right;
        //返回哑节点的下个结点，就是目标链表了
        return res.next;
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/sort-list/solution/sort-list-gui-bing-pai-xu-lian-biao-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210512051559690](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512051600.png)

![image-20210512050625892](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512050627.png)

#### （2）自底向上归并排序

![Picture1.png](https://pic.leetcode-cn.com/c1d5347aa56648afdec22372ee0ed13cf4c25347bd2bb9727b09327ce04360c2-Picture1.png)

###### 断链操作

![image-20210512053522042](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512053523.png)



###### 代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        int length = getLength(head);
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
       //步长要小于链表长度，不能等于，不然就是整个链表了，没有拆分，没有意义了
        for(int step = 1; step < length; step*=2){ //依次将链表分成1块，2块，4块...
            //每次变换步长，pre指针和cur指针都初始化在链表头,dummy的后面的链表都是上个步长
            //排序好的，如图，step=1的时候，两两合并得到的链表是3>4>2>6>5>7>1,这个是接在
            //dummy后面的，每次循环开始，又把pre指针初始化到开头重新对当前的step进行分割合并
            ListNode pre = dummy; 
            ListNode cur = dummy.next;
            while(cur!=null){
                ListNode h1 = cur; //第一部分头 （第二次循环之后，cur为剩余部分头，不断往后把链表按照步长step分成一块一块...）
                ListNode h2 = split(h1,step);  //第二部分头
                cur = split(h2,step); //剩余部分的头
                ListNode temp = merge(h1,h2); //将一二部分排序合并
                pre.next = temp; //将前面的部分与排序好的部分连接
                while(pre.next!=null){
                    pre = pre.next; //把pre指针移动到排序好的部分的末尾
                }
            }
        }
        return dummy.next;
    }
    public int getLength(ListNode head){
    //获取链表长度
        int count = 0;
        while(head!=null){
            count++;
            head=head.next;
        }
        return count;
    }
    public ListNode split(ListNode head,int step){
        //断链操作 返回第二部分链表头
        if(head==null)  return null;
        ListNode cur = head;
        //因为i是从1开始的，就是从链表的第一个结点开始走，即初始长度就是1了，所以要小于步长，那		 //割出来的链表长度
        //就是步长了，这里一定不能少了cur.next!=null，否者空指针了
        for(int i=1; i<step && cur.next!=null; i++){
            cur = cur.next;
        }
        ListNode right = cur.next;
        cur.next = null; //切断连接
        return right;
    }
    public ListNode merge(ListNode h1, ListNode h2){
    //合并两个有序链表
        ListNode head = new ListNode(-1);
        ListNode p = head;
        while(h1!=null && h2!=null){
            if(h1.val < h2.val){
                p.next = h1;
                h1 = h1.next;
            }
            else{
                p.next = h2;
                h2 = h2.next;
            }
            p = p.next;           
        }
        if(h1!=null)    p.next = h1;
        if(h2!=null)    p.next = h2;

        return head.next;     
    }
}

作者：cherry-n1
链接：https://leetcode-cn.com/problems/sort-list/solution/pai-xu-lian-biao-di-gui-die-dai-xiang-jie-by-cherr/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210512064636565](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512064637.png)

![image-20210512064654582](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512064655.png)

## 197、[乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

![image-20210512070223857](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512070224.png)

### 思路

#### （1）动态规划

> 这题是求数组中子区间的最大乘积，对于乘法，我们需要注意，负数乘以负数，会变成正数，所以解这题的时候我们需要维护两个变量，当前的最大值，以及最小值，最小值可能为负数，但没准下一步乘以一个负数，当前的最大值就变成最小值，而最小值则变成最大值了。

![image-20210512073351129](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512073352.png)

![image-20210512073620411](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512073621.png)

![image-20210512073633278](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512073634.png)

###### 代码

```java
public class Solution {

    public int maxProduct(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }

        // dp[i][0]：以 nums[i] 结尾的连续子数组的最小值
        // dp[i][1]：以 nums[i] 结尾的连续子数组的最大值
        int[][] dp = new int[len][2];
        dp[0][0] = nums[0];
        dp[0][1] = nums[0];
        for (int i = 1; i < len; i++) {
            if (nums[i] >= 0) {
                dp[i][0] = Math.min(nums[i], nums[i] * dp[i - 1][0]);
                dp[i][1] = Math.max(nums[i], nums[i] * dp[i - 1][1]);
            } else {
                dp[i][0] = Math.min(nums[i], nums[i] * dp[i - 1][1]);
                dp[i][1] = Math.max(nums[i], nums[i] * dp[i - 1][0]);
            }
        }

        // 只关心最大值，需要遍历
        int res = dp[0][1];
        for (int i = 1; i < len; i++) {
            res = Math.max(res, dp[i][1]);
        }
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-product-subarray/solution/dong-tai-gui-hua-li-jie-wu-hou-xiao-xing-by-liweiw/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210512073752975](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512073754.png)

![image-20210512073805759](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512073806.png)

#### （2）动态规划滚动变量

![image-20210512083327484](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512083328.png)

###### 代码

```java
public class Solution {

    public int maxProduct(int[] nums) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }
	    //初始化最大和最小值就是第一个数
        int preMax = nums[0];
        int preMin = nums[0];

        // 滚动变量
        int curMax;
        int curMin;

        int res = nums[0];
        for (int i = 1; i < len; i++) {
            if (nums[i] >= 0) {
                //当前的最大值其实就跟之前乘积最大值有关，可以带上之前的乘积，或者直接用当前的
                //值来当成最大值，说明之前的乘积起到了副作用，直接抛弃
                curMax = Math.max(preMax * nums[i], nums[i]);
                curMin = Math.min(preMin * nums[i], nums[i]);
            } else {
                curMax = Math.max(preMin * nums[i], nums[i]);
                curMin = Math.min(preMax * nums[i], nums[i]);
            }
            res = Math.max(res, curMax);

            // 赋值滚动变量
            preMax = curMax;
            preMin = curMin;
        }
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/maximum-product-subarray/solution/dong-tai-gui-hua-li-jie-wu-hou-xiao-xing-by-liweiw/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210512083435615](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512083436.png)

![image-20210512084132797](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512084133.png)

#### （3）判断负数的个数

![image-20210512085125102](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512085126.png)

![image-20210512085313768](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512085314.png)

###### 代码

```java
public int maxProduct(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }

    int max = 1;
    int res = nums[0];
    for (int i = 0; i < nums.length; i++) {
        //这个是乘以当前元素后的最大值
        max *= nums[i];
        //res表示没有乘以当前元素的之前的最大值
        res = Math.max(res, max);
        //如果碰到0了，那就重置max，重置max，意味着计算一个新的连续子数组的最大乘积了
        if (max == 0) {
            max = 1;
        }

    }
    max = 1;
    for (int i = nums.length - 1; i >= 0; i--) {
        max *= nums[i];
        res = Math.max(res, max);
        if (max == 0) {
            max = 1;
        }
    }

    return res;
}

作者：windliang
链接：https://leetcode-cn.com/problems/maximum-product-subarray/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--36/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210512085808572](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512085809.png)

![image-20210512090045640](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512090046.png)

## 198、[打家劫舍](https://leetcode-cn.com/problems/house-robber/)

![image-20210512090301165](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210512090302.png)

### 思路

#### （1）动态规划

![image-20210513023310520](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513023311.png)



![image-20210513023406553](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513023407.png)

![image-20210513023549485](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513023550.png)

![image-20210513023655135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513023656.png)





###### 代码

```java
public int rob(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    // 子问题：
    // f(k) = 偷 [0..k) 房间中的最大金额

    // f(0) = 0
    // f(1) = nums[0]
    // f(k) = max{ rob(k-1), nums[k-1] + rob(k-2) }

    int N = nums.length;
    //这里是因为有前0间到前N间的选法，所以共有N+1个子问题
    int[] dp = new int[N+1];
    dp[0] = 0;
    dp[1] = nums[0];
    //求解前K间的问题
    for (int k = 2; k <= N; k++) {
        //当前数组元素是k-1下标的元素，分为选这个和不选这个，选的话，那就是前K-2的dp间隔一个
        //加上当前元素的结果
        dp[k] = Math.max(dp[k-1], nums[k-1] + dp[k-2]);
    }
    return dp[N];
}

作者：nettee
链接：https://leetcode-cn.com/problems/house-robber/solution/dong-tai-gui-hua-jie-ti-si-bu-zou-xiang-jie-cjavap/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210513022921105](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513022922.png)

###### 空间优化

![image-20210513023839286](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513023840.png)

![空间优化前后对比（动图）](https://pic.leetcode-cn.com/3dcbb1028ed9cdac95fdc8c8348ccc6f2e4c50b3fd8222e5690257d6b495090a.gif)

![img](https://pic.leetcode-cn.com/ed3378a4d184a4425282aacbcbb7f043887d8821d67e5e2c0f60ba03a63d6bb1-Picture6.png)



```java
public int rob(int[] nums) {
    int prev = 0;
    int curr = 0;

    // 每次循环，计算“偷到当前房子为止的最大金额”
    for (int i : nums) {
        // 循环开始时，curr 表示 dp[k-1]，prev 表示 dp[k-2]
        // dp[k] = max{ dp[k-1], dp[k-2] + i }
        int temp = Math.max(curr, prev + i);
        prev = curr;
        //temp表示最后一位的最大结果值，所以要返回curr
        curr = temp;
        // 循环结束时，curr 表示 dp[k]，prev 表示 dp[k-1]
    }

    return curr;
}

作者：nettee
链接：https://leetcode-cn.com/problems/house-robber/solution/dong-tai-gui-hua-jie-ti-si-bu-zou-xiang-jie-cjavap/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210513024253175](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513024254.png)

![image-20210513024407948](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513024408.png)

## 199、[实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

![image-20210513024813264](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513024814.png)

### 思路

#### （1）单词搜索

![image-20210513032216864](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513032217.png)

![image-20210513225732523](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513225733.png)

![image-20210513225846081](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513225846.png)

![image-20210513230045927](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513230047.png)

###### 代码

```java
class Trie {
    //这个是当前的字符的直接相邻的下个字符的数组
    private Trie[] children;
    //判断当前字符是不是某个字符串的结尾
    private boolean isEnd;

    public Trie() {
        //初始化26个格子，装26个字母，索引是0-25
        children = new Trie[26];
        isEnd = false;
    }
    
    public void insert(String word) {
        //获取前缀树
        Trie node = this;
        for (int i = 0; i < word.length(); i++) {
            char ch = word.charAt(i);
            //计算当前字符在前缀树中的索引，如果这个位置有Trie，说明当前的字符已经插过了
            int index = ch - 'a';
            if (node.children[index] == null) {
                //当前的位置没有Trie，说明没插过，就插入一个Trie表示当前位置有这个字符插入了
                node.children[index] = new Trie();
            }
            //这个把当前字符的前缀树作为下个搜索的结点，Trie中的chidrean数组其实就是插入单词的             //当前的字符的后面的字符
            node = node.children[index];
        }
        //表示当前的字符是某个字符串的结尾
        node.isEnd = true;
    }
    
    public boolean search(String word) {
        //主要看当前的字符是不是结尾字符，还是说只是当前单词的一个前缀而已
        Trie node = searchPrefix(word);
        return node != null && node.isEnd;
    }
    
    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }

    private Trie searchPrefix(String prefix) {
        Trie node = this;
        for (int i = 0; i < prefix.length(); i++) {
            char ch = prefix.charAt(i);
            int index = ch - 'a';
            //这里没搜到，说明当前字符后面没有字符了，没有完全匹配prefix，直接返回false表示
            //不匹配
            if (node.children[index] == null) {
                return null;
            }
            node = node.children[index];
        }
        //这里是返回最后的字符的前缀树
        return node;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/implement-trie-prefix-tree/solution/shi-xian-trie-qian-zhui-shu-by-leetcode-ti500/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210513230322432](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513230323.png)

![image-20210513232208299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513232210.png)

## 200、[最大正方形](https://leetcode-cn.com/problems/maximal-square/)

![image-20210513233553162](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513233554.png)

![image-20210513233609888](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210513233610.png)

### 思路

#### （1）动态规划

![image-20210514020225185](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210514020226.png)

![image-20210514020311500](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210514020312.png)

###### 代码

```java
public int maximalSquare(char[][] matrix) {
    // base condition
    if (matrix == null || matrix.length < 1 || matrix[0].length < 1) return 0;

    int height = matrix.length;
    int width = matrix[0].length;
    int maxSide = 0;

    // 相当于已经预处理新增第一行、第一列均为0
    int[][] dp = new int[height + 1][width + 1];

    for (int row = 0; row < height; row++) {
        for (int col = 0; col < width; col++) {
            //一定是当前的值是1才要去算正方形的最大面积
            if (matrix[row][col] == '1') {
                dp[row + 1][col + 1] = Math.min(Math.min(dp[row + 1][col], dp[row][col + 1]), dp[row][col]) + 1;
                maxSide = Math.max(maxSide, dp[row + 1][col + 1]);
            }
        }
    }
    return maxSide * maxSide;
}

作者：lzhlyle
链接：https://leetcode-cn.com/problems/maximal-square/solution/li-jie-san-zhe-qu-zui-xiao-1-by-lzhlyle/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210514015338471](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210514015339.png)

![image-20210514015623347](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210514015624.png)

###### 代码2空间优化

> 1. 每次循环前，`dp[0...j-1]`保存的是当前行的最大边长，`dp[j...cols]`保存的是上一行的最大边长。
> 2. 把上一行`dp[j]`暂存到`temp`里面。
> 3. 然后在`dp[j-1]`（左侧的的最大边长）、`dp[j]`（上侧的的最大边长）、`pre`（左上侧的最大边长）里面取最小的，再加`1`就是，以`matrix[i][j]`为右下角正方形的最大边长。
> 4. 把暂存的`temp`放到`pre`里，作为下一次循环左上侧的最大边长。

![image-20210515144825835](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515144827.png)

```java
// 终版代码
public int maximalSquare(char[][] matrix) {
    if (matrix == null || matrix.length < 1 || matrix[0].length < 1) return 0;

    int height = matrix.length;
    int width = matrix[0].length;
    int maxSide = 0;
	//多加一行，一维的话，那就是每行遍历，只看当前行的前面一行当前列的dp值，和当前行的前一列的dp
    //值，加上左上角的最大边长里面选一个最小的即可，dp表示的就是当前行某个格子为右下角的正方形的     //最大边长
    int[] dp = new int[width + 1];

    for (char[] chars : matrix) {
        int northwest = 0; // 个人建议放在这里声明，而非循环体外
        for (int col = 0; col < width; col++) {
            //上一行的当前列的值会成为当前行下个列左上角，提前保存一下，防止被覆盖
            int nextNorthwest = dp[col + 1];
            if (chars[col] == '1') {
                dp[col + 1] = Math.min(Math.min(dp[col], dp[col + 1]), northwest) + 1;
                maxSide = Math.max(maxSide, dp[col + 1]);
                //当前行的当前列一定要更新当前格子的最大边长，因为要成为下一行的左上角参与计算
            } else dp[col + 1] = 0;
            //更新当前行下一列的左上角
            northwest = nextNorthwest;
        }
    }
    return maxSide * maxSide;
}

作者：lzhlyle
链接：https://leetcode-cn.com/problems/maximal-square/solution/li-jie-san-zhe-qu-zui-xiao-1-by-lzhlyle/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210515150558218](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515150559.png)

![image-20210515150626027](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515150626.png)

#### （2）没细看

```java
public int maximalSquare(char[][] matrix) {
    if (matrix.length == 0) {
        return 0;
    }
    // 保存以当前数字结尾的连续 1 的个数
    int[][] width = new int[matrix.length][matrix[0].length];
    // 记录最大边长
    int maxSide = 0;
    // 遍历每一行
    for (int row = 0; row < matrix.length; row++) {
        for (int col = 0; col < matrix[0].length; col++) {
            // 更新 width
            if (matrix[row][col] == '1') {
                if (col == 0) {
                    width[row][col] = 1;
                } else {
                    width[row][col] = width[row][col - 1] + 1;
                }
            } else {
                width[row][col] = 0;
            }
            // 当前点作为正方形的右下角进行扩展
            int curWidth = width[row][col];
            // 向上扩展行
            for (int up_row = row; up_row >= 0; up_row--) {
                int height = row - up_row + 1;
                if (width[up_row][col] <= maxSide || height > curWidth) {
                    break;
                }
                maxSide = Math.max(height, maxSide);
            }
        }
    }
    return maxSide * maxSide;
}

作者：windliang
链接：https://leetcode-cn.com/problems/maximal-square/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--46/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 201、[除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)

![image-20210515151351979](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515151353.png)

### 思路

#### （1）左边乘以右边

![image-20210515153559466](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515153600.png)

![img](https://assets.leetcode-cn.com/solution-static/238/1.PNG)

![img](https://assets.leetcode-cn.com/solution-static/238/2.PNG)

![img](https://assets.leetcode-cn.com/solution-static/238/3.PNG)

![img](https://assets.leetcode-cn.com/solution-static/238/4.PNG)





![img](https://assets.leetcode-cn.com/solution-static/238/5.PNG)

![img](https://assets.leetcode-cn.com/solution-static/238/10.PNG)



```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] res = new int[nums.length];
        int k = 1;
        for(int i = 0; i < res.length; i++){
            //i=0的时候，左边是没有值的，乘积就是1，当i=4的时候，看下一行，k是之前0-3位置的乘积
            //把前面的数的乘积赋给当前位置
            res[i] = k;
            k = k * nums[i]; // 此时数组存储的是除去当前元素左边的元素乘积
        }
        //重置k，k表示当前数右边的乘积，反向遍历，从右边开始，i=最后边的时候，右边没有值，乘积是
        //1，最后就是当前的数左边*右边了
        k = 1;
        for(int i = res.length - 1; i >= 0; i--){
            res[i] *= k; // k为该数右边的乘积。
            k *= nums[i]; // 此时数组等于左边的 * 该数右边的。
        }
        return res;
    }
}

作者：LDouble
链接：https://leetcode-cn.com/problems/product-of-array-except-self/solution/cheng-ji-dang-qian-shu-zuo-bian-de-cheng-ji-dang-q/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210515153229334](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515153230.png)

![image-20210515153410970](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515153411.png)

![image-20210515153442694](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515153443.png)

## 202、[完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

![image-20210515153850422](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515153851.png)

### 思路

#### （1）递归

![image-20210515154908853](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515154910.png)

```java
public int numSquares(int n) {
    return numSquaresHelper(n);
}

private int numSquaresHelper(int n) {
    if (n == 0) {
        return 0;
    }
    int count = Integer.MAX_VALUE;
    //依次减去一个平方数，这里是可以等于n的，那样就直接返回一种了
    for (int i = 1; i * i <= n; i++) {
        //选最小的
        count = Math.min(count, numSquaresHelper(n - i * i) + 1);
    }
    return count;
}

作者：windliang
链接：https://leetcode-cn.com/problems/perfect-squares/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--51/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）备忘录递归

当然上边的会造成超时，很多解会重复的计算，之前也遇到很多这种情况了。我们需要 memoization 技术，也就是把过程中的解利用 HashMap 全部保存起来即可。

```java
public int numSquares(int n) {
    return numSquaresHelper(n, new HashMap<Integer, Integer>());
}

private int numSquaresHelper(int n, HashMap<Integer, Integer> map) {
    if (map.containsKey(n)) {
        return map.get(n);
    }
    if (n == 0) {
        return 0;
    }
    int count = Integer.MAX_VALUE;
    for (int i = 1; i * i <= n; i++) {
        count = Math.min(count, numSquaresHelper(n - i * i, map) + 1);
    }
    map.put(n, count);
    return count;
}

作者：windliang
链接：https://leetcode-cn.com/problems/perfect-squares/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--51/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210515155549172](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515155550.png)

#### （3）动态规划

![image-20210515220847769](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515220848.png)

> 理解了解法一的话，很容易改写成动态规划。递归相当于先压栈压栈然后出栈出栈，动态规划可以省去压栈的过程。
>
> 动态规划的转移方程就对应递归的过程，动态规划的初始条件就对应递归的出口。
>
> 





```java
public int numSquares(int n) {
    int dp[] = new int[n + 1];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0; 
    //依次求出 1, 2... 直到 n 的解
    for (int i = 1; i <= n; i++) {
        //依次减去一个平方数，这里是遍历所有的小于当前数的平方数
        for (int j = 1; j * j <= i; j++) {
            //看看这里面那个所使用的平方数最少，后面那个dp[i]是减去上个j*j后的对应的需要多少个
            //正数，所以就是取某个i，第二层遍历先获取等于当前i所需的最小的平方数
            dp[i] = Math.min(dp[i], dp[i - j * j] + 1);
        }
    }
    return dp[n];
}

作者：windliang
链接：https://leetcode-cn.com/problems/perfect-squares/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--51/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210515221746394](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515221746.png)

#### （4）动态规划优化

![image-20210515222851845](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515222852.png)

![image-20210515222943143](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515222943.png)

![image-20210515223137290](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515223137.png)



```java
static ArrayList<Integer> dp = new ArrayList<>();
public int numSquares(int n) {
    //第一次进入将 0 加入
    if(dp.size() == 0){
        dp.add(0);
    }
    //之前是否计算过 n，因为测试用例会使用这个类一次调用次，所以对一些dp的结果做个缓存
    //这里是<=,因为第一位被占了，所以=n的时候，也说明这个数还没计算，比如n=5，dp肯定算到4了
    //index为0-4，size=5，但是这个时候还没算n=5的结果
    if(dp.size() <= n){
        //接着之前最后一个值开始计算
        for (int i = dp.size(); i <= n; i++) {
            int min = Integer.MAX_VALUE;
            //这里一定是j*j小于，不是单个j
            for (int j = 1; j * j <= i; j++) {
                min = Math.min(min, dp.get(i - j * j) + 1);
            }
            dp.add(min);
        }
    }
    return dp.get(n);
}

作者：windliang
链接：https://leetcode-cn.com/problems/perfect-squares/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--51/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210515225401123](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515225401.png)

![image-20210515223811169](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515223811.png)

#### （5）BFS

![image-20210515224858478](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210515224858.png)

![image-20210516010219884](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516010220.png)

```java
public int numSquares(int n) {
    Queue<Integer> queue = new LinkedList<>();
    HashSet<Integer> visited = new HashSet<>();
    //n在第0层
    int level = 0;
    queue.add(n);
    while (!queue.isEmpty()) {
        int size = queue.size();
        level++; // 开始生成下一层，这个是下一层的层序，从0开始，返回的是最后一层的层序
        for (int i = 0; i < size; i++) {
            int cur = queue.poll();
            //依次减 1, 4, 9... 生成下一层的节点
            for (int j = 1; j * j <= cur; j++) {
                int next = cur - j * j;
                // 因为是广度优先遍历，顺序遍历每一行，所以当节点差出现0时，此时一定是最短的路				//径。因为最后差还不是0的，肯定还要加平方数，注意，这个时候是取路径线上的数字，				//取节点的数据，所以是3
                if (next == 0) {
                    return level;
                }
                // 当再次出现时没有必要加入，因为在该节点的路径长度肯定不小于第一次出现的路径长
                //因为是层次遍历，所以再出现的话，就可以忽略了
                if (!visited.contains(next)) {
                    queue.offer(next);
                    visited.add(next);
                }
            }
        }
    }
    return -1;
}

作者：windliang
链接：https://leetcode-cn.com/problems/perfect-squares/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--51/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516011922421](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516011922.png)

![image-20210516011938114](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516011938.png)

## 203、[合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

![image-20210516012242402](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516012242.png)

### 思路

#### （1）优先队列

![image-20210516013236302](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516013236.png)

![2.jpg](https://pic.leetcode-cn.com/6a29e6a27232b5d42201b57c3ae9b256293b87a291f981c8a0f06e88e50c4379-2.jpg)

###### 代码

```java
class Solution {
	public ListNode mergeKLists(ListNode[] lists) {
		if(lists==null || lists.length==0) {
			return null;
		}
		//创建一个堆，并设置元素的排序方式
		PriorityQueue<ListNode> queue = new PriorityQueue(new Comparator<ListNode>() {
			public int compare(ListNode o1, ListNode o2) {
				return (o1.val - o2.val);
			}
		});
		//遍历链表数组，然后将每个链表的每个节点都放入堆中
		for(int i=0;i<lists.length;i++) {
			while(lists[i] != null) {
                //其实这里加入的结点还保留着原链表下个结点的地址，但是不影响，后面会重新设置这个
                //next指针指向
				queue.add(lists[i]);
				lists[i] = lists[i].next;
			}
		}
		ListNode dummy = new ListNode(-1);
		ListNode head = dummy;
		//从堆中不断取出元素，并将取出的元素串联起来
		while( !queue.isEmpty() ) {
			dummy.next = queue.poll();
			dummy = dummy.next;
		}
		dummy.next = null;
		return head.next;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/duo-tu-yan-shi-23-he-bing-kge-pai-xu-lian-biao-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516014113135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516014113.png)

#### （2）优先队列优化

![image-20210516014647313](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516014647.png)

![5.gif](https://pic.leetcode-cn.com/1d4fb6358f39ee7b4ad0b75119352a0fba44c550af0c310d594ae529717cbf3d-5.gif)

###### 代码

```java
class Solution {
	public ListNode mergeKLists(ListNode[] lists) {
		if(lists==null || lists.length==0) {
			return null;
		}
		//创建一个小根堆，并定义好排序函数
		PriorityQueue<ListNode> queue = new PriorityQueue(new Comparator<ListNode>() {
			public int compare(ListNode o1, ListNode o2) {
				return (o1.val - o2.val);
			}
		});
		ListNode dummy = new ListNode(-1);
		ListNode cur = dummy;
		//这里跟上一版不一样，不再是一股脑全部放到堆中
		//而是只把k个链表的第一个节点放入到堆中
		for(int i=0;i<lists.length;i++) {
			ListNode head = lists[i];
			if(head!=null) {
				queue.add(head);
			}
		}
		//之后不断从堆中取出节点，如果这个节点还有下一个节点，
		//就将下个节点也放入堆中
		while(queue.size()>0) {
			ListNode node = queue.poll();
			cur.next = node;
			cur = cur.next;
			if(node.next!=null) {
				queue.add(node.next);
			}
		}
		cur.next = null;
		return dummy.next;
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/duo-tu-yan-shi-23-he-bing-kge-pai-xu-lian-biao-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析



![image-20210516015654325](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516015654.png)

#### （3）两两合并

![image-20210516015822491](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516015822.png)

![z.jpg](https://pic.leetcode-cn.com/a18aa5111445bc4b70adc7833bd31d14d7882ead9194e4304c0aaa44a55f92ad-z.jpg)

###### 代码

```java
class Solution {
	public ListNode mergeKLists(ListNode[] lists) {
		if(lists==null || lists.length==0) {
			return null;
		}
		//将lists[0]作为最终合并的链表，然后将list[0]和lists[1]合并成lists[0-1]
		//再将lists[0-1]和lists[2]合并，如此反复最终lists[0]就是最终结果
		ListNode res = lists[0];
		for(int i=1;i<lists.length;i++) {
			res = merge(res,lists[i]);
		}
		return res;
	}
	
	//合并两个有序链表
	private ListNode merge(ListNode a, ListNode b) {
		if(a==null || b==null) {
			return (a==null) ? b : a;
		}
		if(a.val<=b.val) {
            //取a结点为当前的结点，然后继续合并
			a.next = merge(a.next,b);
            //最后返回这个a结点，就是合并的链表的结果了
			return a;
		} else {
			b.next = merge(a,b.next);
			return b;
		}
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/duo-tu-yan-shi-23-he-bing-kge-pai-xu-lian-biao-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516021611306](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516021612.png)

![image-20210516020637041](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516020637.png)

#### （4）分治算法

分治就是不断缩小其规模，再不断合并扩大的过程

![6.jpg](https://pic.leetcode-cn.com/88d261465f1f21288dd23cef2f059297f5d053fc19805458a47ae1b05f3c0703-6.jpg)



> 一开始数组的规模是6，我们找到中间点，将起一分为二，然后再拆分，直到不能再拆分(规模为1时)时便返回。
> 之后开始合并，合并的代码借用了合并两个排序链表的代码。
> 当两个规模最小的链表合并完后，其规模就变大了，然后不断重复这个合并过程，直到最终得到一个有序的链表。

###### 代码

```java
class Solution {
	public ListNode mergeKLists(ListNode[] lists) {
		if(lists==null || lists.length==0) {
			return null;
		}
		return helper(lists,0,lists.length-1);
	}
	
	//通过mid将数组一分为二，并不断缩小规模，当规模为1时返回并开始合并
	//通过合并两个链表，不断增大其规模，整体看就是不断缩小-最后不断扩大的过程
	private ListNode helper(ListNode[] lists, int begin, int end) {
		if(begin==end) {
			return lists[begin];
		}
		int mid = begin+(end-begin)/2;
		ListNode left = helper(lists,begin,mid);
		ListNode right = helper(lists,mid+1,end);
		return merge(left,right);
	}
	
	//合并两个有序链表
	private ListNode merge(ListNode a, ListNode b) {
		if(a==null || b==null) {
			return (a==null) ? b : a;
		}
		if(a.val<=b.val) {
			a.next = merge(a.next,b);
			return a;
		} else {
			b.next = merge(a,b.next);
			return b;
		}
	}
}

作者：wang_ni_ma
链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/duo-tu-yan-shi-23-he-bing-kge-pai-xu-lian-biao-by-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516021417596](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516021418.png)

![image-20210516021633653](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516021635.png)

## 204、[最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

![image-20210516021859492](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516021859.png)

### 思路

#### （1）动态规划

![image-20210516025301883](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516025302.png)

###### 代码

```java
public int longestValidParentheses(String s) {
    int maxans = 0;
    int dp[] = new int[s.length()];
    for (int i = 1; i < s.length(); i++) {
        if (s.charAt(i) == ')') {
            //右括号前边是左括号
            if (s.charAt(i - 1) == '(') {
                //如果i=1的话，那就是 一对括号，结果为2，就是后面的2，前面可以是0
                dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
            //右括号前边是右括号，并且除去前边的合法序列的前边是左括号，这个时候i-1肯定是)了
             //不用再判断了
            } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
            }
            maxans = Math.max(maxans, dp[i]);
        }
    }
    return maxans;
}

作者：windliang
链接：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210516031447783](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516031448.png)

![image-20210516031539980](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516031540.png)

#### （2）使用栈

![image-20210516033514448](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516033514.png)

![image-20210516033550201](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516033550.png)

![image-20210516134329879](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516134332.png)

![img](https://assets.leetcode-cn.com/solution-static/32/1.png)

![img](https://assets.leetcode-cn.com/solution-static/32/2.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3.png)

![img](https://assets.leetcode-cn.com/solution-static/32/4.png)

![img](https://assets.leetcode-cn.com/solution-static/32/5.png)

![img](https://assets.leetcode-cn.com/solution-static/32/6.png)![img](https://assets.leetcode-cn.com/solution-static/32/6.png)

![img](https://assets.leetcode-cn.com/solution-static/32/7.png)

![img](https://assets.leetcode-cn.com/solution-static/32/8.png)

![img](https://assets.leetcode-cn.com/solution-static/32/9.png)

![img](https://assets.leetcode-cn.com/solution-static/32/10.png)

![img](https://assets.leetcode-cn.com/solution-static/32/11.png)

![image.png](https://pic.leetcode-cn.com/10c288c56d6537ec6e3c64ff629339ff8ff755d734f655e360256ceede38a179-image.png)

![image.png](https://pic.leetcode-cn.com/10c288c56d6537ec6e3c64ff629339ff8ff755d734f655e360256ceede38a179-image.png)

![Snipaste_2021-05-16_13-50-23](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516135734.png)

![image.png](https://pic.leetcode-cn.com/87d17e45d074a82f9bc40a23a3d10c313664e220df85f6ca641d7c876c6dbeaa-image.png)

![image.png](https://pic.leetcode-cn.com/01ce423be5799b563b1aa3cb49291f4052558693cfcb5f7a18a9294df728ff41-image.png)

![image-20210516140105706](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516140106.png)

![image-20210516140305141](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516140306.png)

###### 代码

```java
public int longestValidParentheses(String s) {
    int maxans = 0;
    Stack<Integer> stack = new Stack<>();
    stack.push(-1);
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == '(') {
            //这里只是放入下标，栈中都是放下标的
            stack.push(i);
        } else {
            //如果是右括号，都要先出一个，如果出了之后，发现栈空了，那就是哨兵-1也被出了，说明
            //当前的右括号是没有匹配的，把当前的右括号的下标当成新的哨兵入栈
            stack.pop();
            if (stack.empty()) {
                stack.push(i);
            } else {
                maxans = Math.max(maxans, i - stack.peek());
            }
        }
    }
    return maxans;
}

作者：windliang
链接：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516140939799](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516140940.png)

![image-20210516141533941](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516141534.png)

#### （3）两遍扫描匹配

![image-20210516142123831](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516142125.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_1.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_2.png)



![img](https://assets.leetcode-cn.com/solution-static/32/3_3.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_4.png)



![img](https://assets.leetcode-cn.com/solution-static/32/3_5.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_6.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_7.png)

![image-20210516143143377](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143144.png)

![image-20210516143209569](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143210.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_10.png)

![image-20210516143307986](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143308.png)



![image-20210516143410346](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143411.png)



![](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143411.png)



![img](https://assets.leetcode-cn.com/solution-static/32/3_16.png)

![img](https://assets.leetcode-cn.com/solution-static/32/3_17.png)



```java
public int longestValidParentheses(String s) {
    int left = 0, right = 0, maxlength = 0;
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == '(') {
            left++;
        } else {
            right++;
        }
        if (left == right) {
            maxlength = Math.max(maxlength, 2 * right);
        } else if (right >= left) {
            left = right = 0;
        }
    }
    //这步不要忘了
    left = right = 0;
    for (int i = s.length() - 1; i >= 0; i--) {
        if (s.charAt(i) == '(') {
            left++;
        } else {
            right++;
        }
        if (left == right) {
            maxlength = Math.max(maxlength, 2 * left);
        } else if (left >= right) {
            left = right = 0;
        }
    }
    return maxlength;
}

作者：windliang
链接：https://leetcode-cn.com/problems/longest-valid-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210516142615868](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516142616.png)

![image-20210516142649537](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516142651.png)

## 205、[柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

![image-20210516143704928](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516143706.png)

### 思路

#### （1）暴力解法

![image-20210516144430441](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210516144431.png)

###### 代码

```java
public class Solution {

    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        // 特判
        if (len == 0) {
            return 0;
        }

        int res = 0;
        for (int i = 0; i < len; i++) {

            // 找左边最后 1 个大于等于 heights[i] 的下标
            int left = i;
            int curHeight = heights[i];
            while (left > 0 && heights[left - 1] >= curHeight) {
                left--;
            }

            // 找右边最后 1 个大于等于 heights[i] 的索引
            int right = i;
            while (right < len - 1 && heights[right + 1] >= curHeight) {
                right++;
            }

            int width = right - left + 1;
            res = Math.max(res, width * curHeight);
        }
        return res;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/largest-rectangle-in-histogram/solution/bao-li-jie-fa-zhan-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （2）单调栈

![image-20210517025403836](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517025405.png)

![image-20210517031240117](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517031240.png)

![image-20210517035138261](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035139.png)

![image-20210517035323575](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035324.png)



![image-20210517035535360](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035536.png)

![image-20210517035640113](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035641.png)

![image-20210517035826124](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035827.png)

![image-20210517035839451](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035840.png)

![image-20210517035904667](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517035905.png)

###### 代码

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {

    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        if (len == 0) {
            return 0;
        }

        if (len == 1) {
            return heights[0];
        }

        int res = 0;

        int[] newHeights = new int[len + 2];
        newHeights[0] = 0;
        //将指定源数组中的数组从指定位置复制到目标数组的指定位置。 阵列组件的一个子序列被从通过引		//用的源阵列复制src被引用的目标阵列dest 。 复制的组件数量等于length参数。 源阵列中位置	//srcPos至srcPos+length-1的组件分别复制到目标阵列的位置destPos至destPos+length-1 。
        System.arraycopy(heights, 0, newHeights, 1, len);
        newHeights[len + 1] = 0;
        len += 2;
        heights = newHeights;

        Deque<Integer> stack = new ArrayDeque<>(len);
        // 先放入哨兵，在循环里就不用做非空判断
        stack.addLast(0);
        //遍历所有的高度，以每个高度为基准，寻找以当前高度为矩形高度的最大的面积
        for (int i = 1; i < len; i++) {
            //这里是跟头先比较，小于的话代表找到了右边第一个小于当前高度的，这里是while循环
            //就是如果当前的值一直比栈顶的小，就表明找到栈顶的右边界了，栈顶要一直出栈
            while (heights[i] < heights[stack.peekLast()]) {
                //注意栈中存放的都是下标，所以还是要去数组中取值
                int curHeight = heights[stack.pollLast()];
                //注意，这里取的是出栈后的当前栈顶，这个才是左边界，左边第一个小于之前出栈的栈顶				//值
                int curWidth = i - stack.peekLast() - 1;
                //找到了以当前高度为基准的最大的矩形了
                res = Math.max(res, curHeight * curWidth);
            }
            //上面都找到了，还是要把当前值入栈，继续找最大的面积，维护成单调递增的栈
            stack.addLast(i);
        }
        return res;
    }

    public static void main(String[] args) {
        // int[] heights = {2, 1, 5, 6, 2, 3};
        int[] heights = {1, 1};
        Solution solution = new Solution();
        int res = solution.largestRectangleArea(heights);
        System.out.println(res);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/largest-rectangle-in-histogram/solution/bao-li-jie-fa-zhan-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210517032503422](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210517032504.png)

###### 代码2

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        // 这里为了代码简便，在柱体数组的头和尾加了两个高度为 0 的柱体。
        int[] tmp = new int[heights.length + 2];
        System.arraycopy(heights, 0, tmp, 1, heights.length); 
        
        Deque<Integer> stack = new ArrayDeque<>();
        int area = 0;
        for (int i = 0; i < tmp.length; i++) {
            // 对栈中柱体来说，栈中的下一个柱体就是其「左边第一个小于自身的柱体」；
            // 若当前柱体 i 的高度小于栈顶柱体的高度，说明 i 是栈顶柱体的「右边第一个小于栈顶柱体的柱体」。
            // 因此以栈顶柱体为高的矩形的左右宽度边界就确定了，可以计算面积🌶️ ～
            while (!stack.isEmpty() && tmp[i] < tmp[stack.peek()]) {
                int h = tmp[stack.pop()];
                area = Math.max(area, (i - stack.peek() - 1) * h);   
            }
            stack.push(i);
        }

        return area;
    }
}

作者：sweetiee
链接：https://leetcode-cn.com/problems/largest-rectangle-in-histogram/solution/xiang-jie-dan-diao-zhan-bi-xu-miao-dong-by-sweetie/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



## 206、[搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

![image-20210518044131958](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518044133.png)

![image-20210518044159126](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210518044159.png)

### 思路

#### （1）暴力法

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == target) {
                    return true;
                }
            }
        }

        return false;
    }
}

作者：LeetCode
链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/sou-suo-er-wei-ju-zhen-ii-by-leetcode-2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519004400570](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519004401.png)

#### （2）二分查找

![image-20210519002812852](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519002813.png)

```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    for (int i = 0; i < matrix.length; i++) {
        if (matrix[i][0] > target) {
            break;
        }
        if(matrix[i][matrix[i].length - 1] < target){
            continue;
        } 
        int col = binarySearch(matrix[i], target);
        if (col != -1) {
            return true;
        }
    }
    return false;
}

//二分查找
private int binarySearch(int[] nums, int target) {
    int start = 0;
    int end = nums.length - 1;
    while (start <= end) {
        int mid = (start + end) >>> 1;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    return -1;
}

作者：windliang
链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519003200956](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519003201.png)

![image-20210519002917992](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519002918.png)

#### （3）右上角开始

![image-20210519003402752](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519003403.png)

![image-20210519003420729](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519003421.png)

```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    int row = 0;
    int col = matrix[0].length - 1;
    while (row < matrix.length && col >= 0) {
        //target大，那就要增大当前值，肯定往下层走了，这个方向是递增的
        if (target > matrix[row][col]) {
            row++;
        } else if (target < matrix[row][col]) {
            //target小，那要减小当前的值，肯定往左走，这个方向是递减的
            col--;
        } else {
            return true;
        }
    }
    return false;
}

作者：windliang
链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210519004103686](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519004104.png)

![image-20210519004450693](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519004451.png)

#### （4）分而治之中心点

我的理解的话，算是一种变形的二分法。

二分法的思想就是，目标值和中点值进行比较，然后可以丢弃一半的元素。

这道题的话是矩阵，如果我们找到矩阵的中心，然后和目标值比较看能不能丢弃一些元素。

```json
如下图，中心位置是 9
[1,   4,  7, 11, 15],
[2,   5,  8, 12, 19],
[3,   6, /9/,16, 22],
[10, 13, 14, 17, 24],
[18, 21, 23, 26, 30]

通过中心位置, 我们可以把原矩形分成四个矩形, 左上, 右上, 左下, 右下
[1,   4,  7   [11, 15  
 2,   5,  8    12, 19  
 3,   6, /9/]  16, 22] 
 
[10, 13, 14   [17, 24
[18, 21, 23]   26, 30]

如果 target = 10,
此时中心值小于目标值，左上角是四个里面矩阵里最小的，左上角矩形中所有的数都小于目标值，我们可以丢弃左上角的矩形，继续从剩下三个矩形中寻找

如果 target = 5,
此时中心值大于目标值，右下角是矩阵里最大的值，右下角矩形中所有的数都大于目标值，那么我们可以丢弃右下角的矩形，继续从剩下三个矩形中寻找 

作者：windliang
链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210519201215446](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210519201242.png)

###### 代码

```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    return searchMatrixHelper(matrix, 0, 0, matrix[0].length - 1, matrix.length - 1, matrix[0].length - 1, matrix.length - 1, target);
}

private boolean searchMatrixHelper(int[][] matrix, int x1, int y1, int x2, int y2, int xMax, int yMax, int target) {
    //只需要判断左上角坐标即可
    if (x1 > xMax || y1 > yMax) {
        return false;
    }
    
    //x 轴代表的是列，y 轴代表的是行
    if(x1 == x2 && y1 == y2){
        return matrix[y1][x1] == target;
    }
    int m1 = (x1 + x2) >>> 1;
    int m2 = (y1 + y2) >>> 1;
    if (matrix[m2][m1] == target) {
        return true;
    }
    if (matrix[m2][m1] < target) {
        // 右上矩阵
        return searchMatrixHelper(matrix, m1 + 1, y1, x2, m2, x2, y2, target) ||
            // 左下矩阵
            searchMatrixHelper(matrix, x1, m2 + 1, m1, y2, x2, y2, target) ||
            // 右下矩阵
            searchMatrixHelper(matrix, m1 + 1, m2 + 1, x2, y2, x2, y2, target);

    } else {
        // 右上矩阵
        return searchMatrixHelper(matrix, m1 + 1, y1, x2, m2, x2, y2, target) ||
            // 左下矩阵
            searchMatrixHelper(matrix, x1, m2 + 1, m1, y2, x2, y2, target) ||
            // 左上矩阵
            searchMatrixHelper(matrix, x1, y1, m1, m2, x2, y2, target);
    }
}

作者：windliang
链接：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析





## 207、[最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

![image-20210521012709545](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521012710.png)

### 思路

#### （1）动态规划

![image-20210521013935684](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521013936.png)

![image-20210521014418431](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521014420.png)

![image-20210521020248514](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521020249.png)

![image-20210521020350608](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521020351.png)







###### 代码



```java
class Solution {
    public int maxProfit(int[] prices) {
        int n=prices.length;
        if(n<=1) return 0;


        //，
        //
        //
        //
        //
        //
        int [][] dp=new int[n][3];
        dp[0][0]=0;
        dp[0][1]=-1*prices[0];
        dp[0][2]=0;

       

        for(int i=1;i<n;i++){//从[1]...[n-1]
          dp[i][0]=Math.max(dp[i-1][0],dp[i-1][2]);
            dp[i][1]=Math.max(dp[i-1][1],dp[i-1][0]-prices[i]);
            dp[i][2]=dp[i-1][1]+prices[i];

        }




        return Math.max(dp[n-1][0],dp[n-1][2]);

       
       

    }
}

作者：jin-ai-yi
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/solution/fei-zhuang-tai-ji-de-dpjiang-jie-chao-ji-tong-su-y/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210521015543570](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521015544.png)

![image-20210521015942460](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521015944.png)

#### （2）空间优化

由于状态值就 3 个，并且只关心最后 1 天的状态值，还可以使用滚动变量的方式把状态表格优化到一行。不过为了使得状态转移正确进行，需要声明两个变量，空间开销等价于 参考代码 2。

```java
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        //如果只有一个元素，说明只能买入或者不买，收益肯定是不买入号呀，因为股票卖不出去
        if (len < 2) {
            return 0;
        }

        int[] dp = new int[3];

        dp[0] = 0;
        dp[1] = -prices[0];
        dp[2] = 0;

        int pre0 = dp[0];
        int pre1 = dp[1];
        int pre2 = dp[2];

        for (int i = 1; i < len; i++) {
            //后面的dp[0]是上次循环i-1的值，pre2是i-1天卖出了，导致今天不持股冷冻期
            dp[0] = Math.max(dp[0], pre2);
            //昨天持股直接继承，要不就是今天买入，那就是昨天的收益pre0减去今天的股价
            dp[1] = Math.max(dp[1], pre0 - prices[i]);
            //今天卖出导致不持股，那就是昨天持股的收益dp[1]加上今天的股价
            dp[2] = pre1 + prices[i];
			//这里提前存好这两个变量，不要被覆盖了
            pre0 = dp[0];
            pre1 = dp[1];
            pre2 = dp[2];
        }
        //要想有收益，肯定要卖股票，持有是不会有收益的，只是股价下跌，收益可能是负的
        return Math.max(dp[0], dp[2]);
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/solution/dong-tai-gui-hua-by-liweiwei1419-5/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210521025336153](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521025337.png)

## 208、[零钱兑换](https://leetcode-cn.com/problems/coin-change/)

![image-20210521030220790](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521030221.png)

### 思路

#### （1）动态规划

![image-20210521033433790](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521033434.png)

###### 代码

```java
import java.util.Arrays;

public class Solution {

    public int coinChange(int[] coins, int amount) {
        // 给 0 占位
        int[] dp = new int[amount + 1];

        // 注意：因为要比较的是最小值，这个不可能的值就得赋值成为一个最大值，如果最少硬币数都大于
        //amount了，那肯定是不可能的
        Arrays.fill(dp, amount + 1);

        // 理解 dp[0] = 0 的合理性，单独一枚硬币如果能够凑出面值，符合最优子结构
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                //dp[i - coin] != amount + 1表示之前求的小的amount是有解的
                if (i - coin >= 0 && dp[i - coin] != amount + 1) {
                    dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
                }
            }
        }

        if (dp[amount] == amount + 1) {
            dp[amount] = -1;
        }
        return dp[amount];
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/coin-change/solution/dong-tai-gui-hua-shi-yong-wan-quan-bei-bao-wen-ti-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210521034320306](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521034321.png)

![image-20210521034334958](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521034335.png)

#### （2）递归

使用递归的关键是知道递归函数是用来干什么的，从宏观的角度去理解递归。
直接使用递归超出时间限制

```java
class Solution {
    int res = Integer.MAX_VALUE;
    public int coinChange(int[] coins, int amount) {
        if(coins.length == 0){
            return -1;
        }

        findWay(coins,amount,0);

        // 如果没有任何一种硬币组合能组成总金额，返回 -1。
        if(res == Integer.MAX_VALUE){
            return -1;
        }
        return res;
    }

    public void findWay(int[] coins,int amount,int count){
        if(amount < 0){
            return;
        }
        //只有最后递归到0，才表示当前的硬币组合可以组成零钱
        if(amount == 0){
            res = Math.min(res,count);
        }

        for(int i = 0;i < coins.length;i++){
            //count是记录当前零钱由多少枚硬币组成的，每递归一次，代表增加一枚硬币组成零钱
            findWay(coins,amount-coins[i],count+1);
        }
    }
}


作者：sugar666
链接：https://leetcode-cn.com/problems/coin-change/solution/javadi-gui-ji-yi-hua-sou-suo-dong-tai-gui-hua-by-s/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

#### （3）记忆化搜索

![image-20210521194330420](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521194354.png)

> 可以看出在进行递归的时候，有很多重复的节点要进行操作，这样会浪费很多的时间。
> 使用数组 memo[\ ]memo[ ] 来保存节点的值
> memo[n]memo[n] 表示钱币 nn 可以被换取的最少的硬币数，不能换取就为 -1−1
> findWay 函数的目的是为了找到 amountamount 数量的零钱可以兑换的最少硬币数量，返回其值 intint
>
> 在进行递归的时候，memo[n]被复制了，就不用继续递归了，可以直接的调用
>
> 

```java
class Solution {
    int[] memo;
    public int coinChange(int[] coins, int amount) {
        if(coins.length == 0){
            return -1;
        }
        memo = new int[amount];

        return findWay(coins,amount);
    }
    // memo[n] 表示钱币n可以被换取的最少的硬币数，不能换取就为-1
    // findWay函数的目的是为了找到 amount数量的零钱可以兑换的最少硬币数量，返回其值int
    public int findWay(int[] coins,int amount){
        if(amount < 0){
            return -1;
        }
        //数量为0，说明上层那枚硬币组成了最后的剩余面值了（也可能当前的面值用一枚硬币就可以了）
        //所以返回0
        if(amount == 0){
            return 0;
        }
        // 记忆化的处理，memo[n]用赋予了值，就不用继续下面的循环
        // 直接的返回memo[n] 的最优值，这里是判断！=0,不是判断！=-1,如果面值没有组合结果也要把-1			//返回
        if(memo[amount-1] != 0){
            return memo[amount-1];
        }
        //min是放在for循环外的，要判断每个零钱面值每个coin组合的最少组合结果
        int min = Integer.MAX_VALUE;
        for(int i = 0;i < coins.length;i++){
            //这里是指当前的硬币一枚加上剩下面值的零钱的硬币最小组合数，所以这行有表示一枚硬币的
            //res是剩下面值的最小硬币的数量
            int res = findWay(coins,amount-coins[i]);
            //这里res可以等于0，说明直接由当前的硬币组成了这个零钱，剩下的面值就是0
            if(res >= 0 && res < min){
                min = res + 1; // 加1，是为了加上得到res结果的那个步骤中，兑换的一个硬币
            }
        }
        //缓存当前面值的硬币组合结果，这里放在amount-1位置，因为初始化就amount的数量
        memo[amount-1] = (min == Integer.MAX_VALUE ? -1 : min);
        return memo[amount-1];
    }
}

作者：sugar666
链接：https://leetcode-cn.com/problems/coin-change/solution/javadi-gui-ji-yi-hua-sou-suo-dong-tai-gui-hua-by-s/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210521204756138](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521204757.png)

![image-20210521204809257](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521204812.png)

#### （4）BFS队列

![image-20210521205248418](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521205249.png)

```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

public class Solution {

    public int coinChange(int[] coins, int amount) {
        if (amount == 0) {
            return 0;
        }

        Queue<Integer> queue = new LinkedList<>();
        boolean[] visited = new boolean[amount + 1];

        visited[amount] = true;
        queue.offer(amount);

        // 排序是为了加快广度优先遍历过程中，对硬币面值的遍历，起到剪枝的效果
        Arrays.sort(coins);

        int step = 1;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Integer head = queue.poll();
                for (int coin : coins) {
                    int next = head - coin;
                    // 只要遇到 0，就找到了一个最短路径
                    if (next == 0) {
                        return step;
                    }

                    if (next < 0) {
                        // 由于 coins 升序排序，后面的面值会越来越大，剪枝
                        break;
                    }

                    if (!visited[next]) {
                        queue.offer(next);
                        // 添加到队列的时候，就应该立即设置为 true
                        // 否则还会发生重复访问
                        visited[next] = true;
                    }
                }
            }
            step++;
        }
        // 进入队列的顶点都出队，都没有看到 0 ，就表示凑不出当前面值
        return -1;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/coin-change/solution/dong-tai-gui-hua-shi-yong-wan-quan-bei-bao-wen-ti-/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210521205322476](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521205323.png)

![image-20210521205335062](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521205335.png)

## 209、[打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)

![image-20210521205411767](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521205413.png)

### 思路

#### （1）暴力法

![image-20210521210039497](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521210040.png)

```java
public int rob(TreeNode root) {
    if (root == null) return 0;

    int money = root.val;
    if (root.left != null) {
        money += (rob(root.left.left) + rob(root.left.right));
    }

    if (root.right != null) {
        money += (rob(root.right.left) + rob(root.right.right));
    }

    return Math.max(money, rob(root.left) + rob(root.right));
}

作者：reals
链接：https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210521210441983](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210521210443.png)

#### （2）记忆化递归

![image-20210522023638043](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522023704.png)

```java
public int rob(TreeNode root) {
    HashMap<TreeNode, Integer> memo = new HashMap<>();
    return robInternal(root, memo);
}

public int robInternal(TreeNode root, HashMap<TreeNode, Integer> memo) {
    if (root == null) return 0;
    if (memo.containsKey(root)) return memo.get(root);
    int money = root.val;

    if (root.left != null) {
        money += (robInternal(root.left.left, memo) + robInternal(root.left.right, memo));
    }
    if (root.right != null) {
        money += (robInternal(root.right.left, memo) + robInternal(root.right.right, memo));
    }
    int result = Math.max(money, robInternal(root.left, memo) + robInternal(root.right, memo));
    memo.put(root, result);
    return result;
}

作者：reals
链接：https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210522023847958](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522023848.png)

#### （3）动态规划+递归

![image-20210522025036571](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522025037.png)

```java
public int rob(TreeNode root) {
    int[] result = robInternal(root);
    return Math.max(result[0], result[1]);
}

public int[] robInternal(TreeNode root) {
    if (root == null) return new int[2];
    int[] result = new int[2];

    int[] left = robInternal(root.left);
    int[] right = robInternal(root.right);

    result[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
    result[1] = left[0] + right[0] + root.val;

    return result;
}

作者：reals
链接：https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210522024949959](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522024950.png)

![image-20210522025332273](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522025333.png)

## 210、[比特位计数](https://leetcode-cn.com/problems/counting-bits/)

![image-20210522025457793](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522025458.png)

### 思路

#### （1）位运算

![image-20210522030649746](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522030650.png)

这里要知道的是-1，二进制减-1后，不够借位借的是2，前一位再不够，借的也是2，然后借给下一位1，

自己就剩了一个1，这样一直到碰到高位的1，把这个1变成0，这个1后面的0都变成了1

```java
class Solution {
    public int[] countBits(int num) {
        //这里是从0开始算的，所以是n+1
        int[] bits = new int[num + 1];
        for (int i = 0; i <= num; i++) {
            bits[i] = countOnes(i);
        }
        return bits;
    }

    public int countOnes(int x) {
        int ones = 0;
        while (x > 0) {
            x &= (x - 1);
            ones++;
        }
        return ones;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/counting-bits/solution/bi-te-wei-ji-shu-by-leetcode-solution-0t1i/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



###### 复杂度分析

![image-20210522033621915](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522033622.png)

![image-20210522031129559](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522031130.png)

#### （2）动态规划

很简单啊，不用多说，设 dp[i]为 i 的二进制形式的 1 的个数， i 是奇数时，dp[i]=dp[i-1]+1,因为 i是在 i-1 的二进制数上加了个 1 啊； i 是偶数时，dp[i]=dp[i/2],因为i就是把 i/2 往左移（是数左移，末尾 补0）得到的，所以 1 的个数没变



一个数比如a，如果是偶数，那么a比特位1的个数和(a/2)比特位1的个数是一样的，因为一个数是偶数那么他的二进制比他的一半的二进制只是多了一个0而已。

如果是奇数就不一样了，他会比除以2的结果多了一个1（比如9的二进制比4的二进制多一个1，19的二进制比9的二进制多一个1，等等）。



```java
class Solution {
    public int[] countBits(int num) {
         if(num==0) return new int[]{0};
        int [] dp=new int [num+1];
        dp[0]=0;
        for(int i=1;i<=num;i++){
            //偶数的时候，i等于i/2*2，而乘2其实就是左移一位，抬高位次，1的数量是不变的
            if(i%2==0) dp[i]=dp[i/2];
            //偶数的话，其实就是在偶数二进制末尾补1
            else dp[i]=dp[i-1]+1;
        }
        return dp;


    }
}

作者：jin-ai-yi
链接：https://leetcode-cn.com/problems/counting-bits/solution/zhe-ying-gai-shi-dao-easyti-ba-wei-yun-suan-ji-chu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
    public int[] countBits(int num) {
        int[] bits = new int[num + 1];
        for (int i = 1; i <= num; i++) {
            //一个数的比特位1的个数先让他等于他一半的比特位量
            bits[i] = bits[i / 2];
            //如果是奇数还要加1
            if ((i & 1) == 1)
                bits[i]++;
        }
        return bits;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/counting-bits/solution/bi-te-wei-ji-shu-duo-chong-jie-jue-fang-p77tu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

```java
    public int[] countBits(int num) {
        int[] bits = new int[num + 1];
        for (int i = 1; i <= num; i++)
            bits[i] = bits[i >> 1] + (i & 1);
        return bits;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/counting-bits/solution/bi-te-wei-ji-shu-duo-chong-jie-jue-fang-p77tu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

![image-20210522032638135](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522032639.png)

###### 复杂度分析

![image-20210522033642300](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522033644.png)

![image-20210522032258578](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522032259.png)

#### （3）位运算2

![image-20210522033349427](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522033350.png)

```java
    public int[] countBits(int num) {
        int[] bits = new int[num + 1];
        for (int i = 1; i <= num; ++i)
            bits[i] = bits[i & (i - 1)] + 1;
        return bits;
    }

作者：sdwwld
链接：https://leetcode-cn.com/problems/counting-bits/solution/bi-te-wei-ji-shu-duo-chong-jie-jue-fang-p77tu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210522033447868](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522033449.png)

## 211、[字符串解码](https://leetcode-cn.com/problems/decode-string/)

![image-20210522033740202](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522033741.png)

### 思路

#### （1）两个辅助栈

![image-20210522145202044](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145228.png)

![image-20210522145402008](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145402.png)

![image-20210522145442299](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145443.png)

![image-20210522145509211](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145510.png)

![image-20210522145538606](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145539.png)

![image-20210522145720050](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145721.png)

![image-20210522145746951](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522145747.png)

![img](https://pic.leetcode-cn.com/b94aefc640cbf53682697e9114513c73e90dc12dcaf4d57b35d8c8a1e6fcad1e-Picture8.png)

###### 代码

```java
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        LinkedList<Integer> stack_multi = new LinkedList<>();
        LinkedList<String> stack_res = new LinkedList<>();
        for(Character c : s.toCharArray()) {
            //碰到左括号，就入栈res和数字，待碰到下个右括号的时候出栈匹配
            //这个时候数字和字符变量res和multi都置空
            if(c == '[') {
                stack_multi.addLast(multi);
                stack_res.addLast(res.toString());
                multi = 0;
                res = new StringBuilder();
            }
            //碰到右括号，这个时候出栈数字，把两个括号之间的也就是当前的res翻倍，再出栈
            //字符，也就是当前左括号前面的字符拼接
            else if(c == ']') {
                //这里要重新搞个tmp来拼接
                StringBuilder tmp = new StringBuilder();
                int cur_multi = stack_multi.removeLast();
                for(int i = 0; i < cur_multi; i++) tmp.append(res);
                //碰到右括号，就把生成的字符放到res中，这个是已经完成【】匹配的，所以没问题
                //2[abc]3[cd]ef，比如第一个[],可以直接先生成abcabcabc，再去当成res去匹配后面的
                res = new StringBuilder(stack_res.removeLast() + tmp);
            }
            //比如[23a]，这不就是23个a嘛，那首先扫描到2，num = 2，然后再扫描到3，2 * 10 + 3就是			23，这里是为了那种连续多位的数字，因为碰到[ 的加，multi就直接置为0了
            else if(c >= '0' && c <= '9') multi = multi * 10 + Integer.parseInt(c + "");
            else res.append(c);
        }
        return res.toString();
    }
}

作者：jyd
链接：https://leetcode-cn.com/problems/decode-string/solution/decode-string-fu-zhu-zhan-fa-di-gui-fa-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210522150648243](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522150649.png)

![image-20210522151741035](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522151741.png)

#### （2）递归

![image-20210522160018976](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522160020.png)

###### 代码

```java
class Solution {
    //对着上图看，不好理解
    public String decodeString(String s) {
        return dfs(s, 0)[0];
    }
    private String[] dfs(String s, int i) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        while(i < s.length()) {
            if(s.charAt(i) >= '0' && s.charAt(i) <= '9') 
                multi = multi * 10 + Integer.parseInt(String.valueOf(s.charAt(i))); 
            //碰到左括号就递归，一直找到这个左括号的右括号
            else if(s.charAt(i) == '[') {
                //返回的右括号的index和左右括号之间的字符串
                String[] tmp = dfs(s, i + 1);
                //这里很巧妙，直接把i设置为右括号的位置，这样下次循环就是右括号的下个位置了
                //因为递归下一层已经把左括号和右括号之前的字符都走过了，所以当前层就直接跳过这些
                //位置，比如上图4中的，在位置递归了，找到10返回，下个遍历的位置直接是11
                i = Integer.parseInt(tmp[0]);
                //这里是根据左括号前面的数字来加倍括号之间的字符串
                while(multi > 0) {
                    res.append(tmp[1]);
                    multi--;
                }
            }
            else if(s.charAt(i) == ']') 
                //返回右括号的位置和括号之间的字符串
                return new String[] { String.valueOf(i), res.toString() };
            else 
                //这里是一直在拼接括号之间的字符串
                res.append(String.valueOf(s.charAt(i)));
            i++;
        }
        //最后为了递归的返回值，但是没有下标，直接返回结果的字符串
        return new String[] { res.toString() };
    } 
}

作者：jyd
链接：https://leetcode-cn.com/problems/decode-string/solution/decode-string-fu-zhu-zhan-fa-di-gui-fa-by-jyd/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210522160651634](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522160652.png)

![image-20210522162608693](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522162609.png)

## 212、[除法求值](https://leetcode-cn.com/problems/evaluate-division/)

![image-20210522162900059](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522162901.png)

![image-20210522162914460](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522162915.png)

### 思路

#### （1）并查集

感觉有点复杂  有空再研究



## 213、[根据身高重建队列](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

![image-20210522164057775](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522164058.png)

### 思路

#### （1）先排序再插入

> 首先解释一下题目：
> 给定一群人的一个队列，要求重新排序。队列中每个人由一个整数对(h, k)表示，其中h是这个人的身高，k表示在新队列中要求排在这个人前面且身高大于或等于h的人数。注：队列排序即数值排序
>
> 更通俗一点就是：假设有一个队列，已知每个人的身高h和排在该人前面的更高或一样高的人数k，现在给你打乱顺序后的数组，要求恢复原来的队列。
>
> 考虑将这群人依次加入新队列中，加入时需符合k的要求。注意到，一个人的k值实际上与身高更矮的人的位置无关，所以如果身高比他更高的人已经排好队了，这个人加入当前队列的位置就可以根据k值确定了。因此，身高较高的人应该先加入，我们先对队列按身高降序排序。此外，对于相同身高的人，k值较小的人位置在前，优先加入。

![image-20210522172404612](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522172405.png)

![image-20210522173110554](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522173111.png)

![img](https://pic.leetcode-cn.com/1605507691-cNsBDz-image.png)

![img](https://pic.leetcode-cn.com/1605507724-thDrQv-image.png)

![img](https://pic.leetcode-cn.com/1605507782-rqZJRC-image.png)

![img](https://pic.leetcode-cn.com/1605507804-PibNnX-image.png)

![image-20210522173416438](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522173417.png)

![img](https://pic.leetcode-cn.com/1605507852-WCTcSX-image.png)

![img](https://pic.leetcode-cn.com/1605507876-njskmi-image.png)

![img](https://pic.leetcode-cn.com/1605507921-DhmGDp-image.png)

![img](https://pic.leetcode-cn.com/1605507946-UraHvN-image.png)

###### 代码

```java
    /**
     * 解题思路：先排序再插入
     * 1.排序规则：按照先H高度降序，K个数升序排序
     * 2.遍历排序后的数组，根据K插入到K的位置上
     *
     * 核心思想：高个子先站好位，矮个子插入到K位置上，前面肯定有K个高个子，矮个子再插到前面也满足K的要求
     *
     * @param people
     * @return
     */
    public int[][] reconstructQueue(int[][] people) {
        // [7,0], [7,1], [6,1], [5,0], [5,2], [4,4]
        // 再一个一个插入。
        // [7,0]
        // [7,0], [7,1]
        // [7,0], [6,1], [7,1]
        // [5,0], [7,0], [6,1], [7,1]
        // [5,0], [7,0], [5,2], [6,1], [7,1]
        // [5,0], [7,0], [5,2], [6,1], [4,4], [7,1]
        //这里是身高一样，按k升序，否者就按身高降序
        Arrays.sort(people, (o1, o2) -> o1[0] == o2[0] ? o1[1] - o2[1] : o2[0] - o1[0]);

        LinkedList<int[]> list = new LinkedList<>();
        for (int[] i : people) {
            //这里是针对上面排序好的数组，直接再k的位置插入
            list.add(i[1], i);
        }

        return list.toArray(new int[list.size()][2]);
    }


作者：pphdsny
链接：https://leetcode-cn.com/problems/queue-reconstruction-by-height/solution/406-gen-ju-shen-gao-zhong-jian-dui-lie-java-xian-p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210522174416602](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522174417.png)

![image-20210522174639847](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210522174640.png)

## 214、[路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

![image-20210523014223750](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523014226.png)

### 思路

#### （1）前缀和递归回溯

![image-20210523143847833](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523143849.png)

![image-20210523144020083](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523144021.png)

![image-20210523144120480](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523144121.png)

![image-20210523142721950](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523142747.png)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int pathSum(TreeNode root, int sum) {
        // key是前缀和, value是大小为key的前缀和出现的次数
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        // 前缀和为0的一条路径前缀树为0的个数至少是一个
        prefixSumCount.put(0, 1);
        // 前缀和的递归回溯思路
        return recursionPathSum(root, prefixSumCount, sum, 0);
    }

    /**
     * 前缀和的递归回溯思路
     * 从当前节点反推到根节点(反推比较好理解，正向其实也只有一条)，有且仅有一条路径，因为这是一棵树
     * 如果此前有和为currSum-target,而当前的和又为currSum,两者的差就肯定为target了
     * 所以前缀和对于当前路径来说是唯一的，当前记录的前缀和，在回溯结束，回到本层时去除，保证其不影响其他分支的结果
     * @param node 树节点
     * @param prefixSumCount 前缀和Map
     * @param target 目标值
     * @param currSum 当前路径和
     * @return 满足题意的解
     */
    private int recursionPathSum(TreeNode node, Map<Integer, Integer> prefixSumCount, int target, int currSum) {
        // 1.递归终止条件
        if (node == null) {
            return 0;
        }
        // 2.本层要做的事情
        int res = 0;
        // 当前路径上的和，前缀和一定是连续节点累加的
        currSum += node.val;

        //---核心代码
        // 看看root到当前节点这条路上是否存在节点前缀和加target为currSum的路径
        // 当前节点->root节点反推，有且仅有一条路径，如果此前有和为currSum-target,而当前的和又为currSum,两者的差就肯定为target了
        // currSum-target相当于找路径的起点，起点的sum+target=currSum，当前点到起点的距离就是target，这个是计算满足等于target的个数
        res += prefixSumCount.getOrDefault(currSum - target, 0);
        // 更新路径上当前节点前缀和的个数,这个是把当前节点的前缀和记录到map中
        prefixSumCount.put(currSum, prefixSumCount.getOrDefault(currSum, 0) + 1);
        //---核心代码

        // 3.进入下一层
        res += recursionPathSum(node.left, prefixSumCount, target, currSum);
        res += recursionPathSum(node.right, prefixSumCount, target, currSum);

        // 4.回到本层，恢复状态，去除当前节点的前缀和数量，对下一层而言，currSum肯定是+1的
        //下层递归完，回到本层，先把currSum的个数减1，因为马上要返回本层的上一层了
        prefixSumCount.put(currSum, prefixSumCount.get(currSum) - 1);
        return res;
    }
}

作者：burning-summer
链接：https://leetcode-cn.com/problems/path-sum-iii/solution/qian-zhui-he-di-gui-hui-su-by-shi-huo-de-xia-tian/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210523145153285](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523145154.png)

![image-20210523145241663](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523145242.png)

#### （2）双重递归

![image-20210523151722899](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523151724.png)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int pathSum(TreeNode root, int sum) {
        if(root == null){
            return 0;
        }
        int result = countPath(root,sum);
        int a = pathSum(root.left,sum);
        int b = pathSum(root.right,sum);
        return result+a+b;

    }
    public int countPath(TreeNode root,int sum){
        if(root == null){
            return 0;
        }
        sum = sum - root.val;
        int result = sum == 0 ? 1:0;
        return result + countPath(root.left,sum) + countPath(root.right,sum);
    }
}

作者：Geralt_U
链接：https://leetcode-cn.com/problems/path-sum-iii/solution/437lu-jing-zong-he-iii-di-gui-fang-shi-by-ming-zhi/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210523152133576](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523152134.png)

## 215、[找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

![image-20210523152709938](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523152710.png)

### 思路

#### （1）滑动窗口

![image-20210523163146505](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523163147.png)

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        char[] arrS = s.toCharArray();
        char[] arrP = p.toCharArray();
        
        // 接收最后返回的结果
        List<Integer> ans = new ArrayList<>();
        
        // 定义一个 needs 数组来看 arrP 中包含元素的个数
        int[] needs = new int[26];
        // 定义一个 window 数组来看滑动窗口中是否有 arrP 中的元素，并记录出现的个数
        int[] window = new int[26]; 
        
        // 先将 arrP 中的元素保存到 needs 数组中，记录的是字符的个数，因为不要求循序，所以
        //只要每个字符的个数符合即可，并且长度一样的话，就是异位词
        for (int i = 0; i < arrP.length; i++) {
            needs[arrP[i] - 'a'] += 1;
        }
        
        // 定义滑动窗口的两端
        int left = 0;
        int right = 0;
        
        // 右窗口开始不断向右移动
        while (right < arrS.length) {
            int curR = arrS[right] - 'a';
            right++;
            // 将右窗口当前访问到的元素 curR 个数加 1 
            window[curR] += 1;
            
            // 当 window 数组中 curR 比 needs 数组中对应元素的个数要多的时候就该移动左窗口指针
            //这里是为了保持窗口内的元素数量始终等于目前字符串的字符数量，而且左侧是肯定已经遍历
            //过的，结果已经记录了，可以放心得缩小不用怕错过结果了，右侧窗口要不断地移动纳入新的
            //字符
            while (window[curR] > needs[curR]) {
                int curL = arrS[left] - 'a';
                left++;
                // 将左窗口当前访问到的元素 curL 个数减 1 
                window[curL] -= 1;            
            }
            
            // 这里将所有符合要求的左窗口索引放入到了接收结果的 List 中
            if (right - left == arrP.length) {
                ans.add(left);
            }
        }
        return ans;
    }
}

作者：Jason_H
链接：https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/solution/20200321438median-by-jasion_han-r/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210523164615139](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523164615.png)

![image-20210523164631335](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210523164632.png)

## 216、[目标和](https://leetcode-cn.com/problems/target-sum/)

![image-20210525041209445](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525041210.png)

### 思路

#### （1）动态规划01背包

![image-20210525044713683](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525044714.png)

![image-20210525044955933](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525044957.png)

![image-20210525045934623](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525045935.png)

###### 代码

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for(int num : nums) sum += num;
        //如果目标和大于了数组的最大和了，那肯定不行，因为数组是正整数数组，奇数也不行，/2就不是
        //正数了，表示不存在这样的背包容量了
        if(target > sum || (target + sum) % 2 == 1) return 0;
        target = (target + sum) / 2;
        //包括0，所以是tareget+1
        int[] dp = new int[target + 1];
        dp[0] = 1;
        //这里是遍历每个num，看看加入当前的num，填充背包容量j的方法数
        for(int num : nums){
            //这里采取倒着遍历，这样不会覆盖dp[j-num]的旧值，背包容量j肯定要大于等于当前的数
            //不然放不进去
            for(int j = target; j >= num; j--){
                dp[j] = dp[j] + dp[j - num];
            }
        }
        return dp[target];
    }
}

作者：edelweisskoko
链接：https://leetcode-cn.com/problems/target-sum/solution/494-mu-biao-he-dong-tai-gui-hua-zhi-01be-78ll/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

###### 复杂度分析

![image-20210525051959732](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525052000.png)

![image-20210525052110028](https://gitee.com/top20chenql/md_imgs/raw/master/img/20210525052110.png)