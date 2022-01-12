# [KMP算法](https://www.cnblogs.com/linfangnan/p/14698654.html)

## BF 算法

### 算法思想

BF 算法，即暴风(Brute Force)算法，不是要匹配字符吗？那我一个一个直接匹配不就好啦。BF 算法的思想就是将目标串 S 的第一个字符与模式串 T 的第一个字符进行匹配，若相等，则继续比较 S 的第二个字符和 T 的第二个字符；若不相等，则比较S的第二个字符和 T 的第一个字符，依次比较下去，直到得出最后的匹配结果。

### 代码实现

```java
public int BF(String s, String t, int pos) {
    /**
* s为目标字符串
* t为匹配字符串
* pos为起始位置
* 算法思想： BF 算法的思想就是将目标串 S 的第一个字符与模式串 T 的第一个字符进行匹配，
*          若相等，则继续比较 S 的第二个字符和 T 的第二个字符；
*          若不相等，则比较S的第二个字符和 T 的第一个字符，依次比较下去，直到得出最后的匹配结果。
*/
    int i = pos;
    int j = 0;

    while (i < s.length() && j < t.length()) {
        if (s.charAt(i) == t.charAt(j)) { // 同时移动i和j
            i++;
            j++;
        } else {
            i = i - j + 1; // i退回当前匹配失败的字符串的第一个字符的下一个
            j = 0; // j回退到0，重新匹配
        }
    }

    if (j >= s.length()) { // 匹配成功，返回起始位置
        return i - j;
    } else {
        return -1;
    }
}
```

## KMP 算法思想

假设我们的目标串是 “abcdefgab……”，模式串是 “abcde&”，那么使用 BF 算法匹配流程如下所示。
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939084.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407105746318-894240129.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939086.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407105841608-1934866898.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939087.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407105934614-1714435104.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939088.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407110845487-654685119.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939089.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407110917227-1199476335.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939090.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407111003011-2000806747.png)
但是在模式串中，第一个字符 “a” 与后面的字符 “bcde&” 都不一样，也就是说对于第一步，前 5 位字符都匹配成功了，那么 “a” 与目标串中的第 2~5 个字符一定不能匹配，那么流程就可以缩减成这样：
[![img](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407105746318-894240129.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407105746318-894240129.png)
[![img](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407111003011-2000806747.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407111003011-2000806747.png)
再看个例子，假设我们的目标串是 “abcababca……”，模式串是 “abcab&”，那么使用 BF 算法匹配流程如下所示。
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939091.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113301698-1038919361.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939092.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113407032-1838125382.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939093.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113435573-784294825.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939094.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113519883-1315051647.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939095.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113542952-1561652860.png)
[![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201111939096.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113617777-1538891735.png)
因为模式串中的第一位和第四位的字符都是 “a”，第二位和第五位字符都是 “b”，而第四位和第五位在第一步的时候已经匹配成功了，因此匹配的过程可以简化为：
[![img](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113301698-1038919361.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113301698-1038919361.png)
[![img](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113617777-1538891735.png)](https://img2020.cnblogs.com/blog/1774310/202004/1774310-20200407113617777-1538891735.png)
那么这种思想就是 KMP 算法，这种算法的思想是为了让不必要的回溯不发生。
KMP 算法是一种改进的字符串匹配算法，由 D.E.Knuth，J.H.Morris 和 V.R.Pratt 提出的，因此人们称它为克努特—莫里斯—普拉特操作（简称 KMP 算法）。KMP算法的核心是利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的。具体实现就是通过一个 next() 函数实现，函数本身包含了模式串的局部匹配信息。KMP 算法的时间复杂度 O(m+n)。

## 代码实现

### 匹配函数

```java
public int kmp(String s,String t,int pos){
    /**
         * pos:从字符串的pos下标开始匹配
         * t:描述模式串t的下标
         */
    int i = pos;
    int j = 0;
    int[] next = new int[t.length()];
    getNext(t,next);

    while(i < s.length() && j < t.length()){
        if(s.charAt(i) == t.charAt(j) ||j == -1){
            i++;
            j++;
        }else {
            j = next[j];
        }
    }

    if(j > t.length()){
        return i - t.length();
    }else {
        return -1;
    }
}
```

### 求 next 数组

```java
void getNext(String t,int next[]){
    int i = 0;
    int j = -1;
    next[0] = -1;

    while(i < t.length()){
        if(j == -1 || t.charAt(i) == t.charAt(j)){ // 匹配前缀和后缀
            i++;
            j++;
            next[i] = j;
        }else { // 字符不相同，回溯
            j = next[j];
        }
    }
}
```

## 参考资料

[BF算法](https://blog.csdn.net/free377096858/article/details/88783339)
[史上最简(详细)KMP算法讲解，看不懂算我输！](https://www.sohu.com/a/336648975_453160)

