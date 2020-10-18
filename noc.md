*   题目一：给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

​     说明：

​     你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

​     示例 1:

​    输入: [2,2,1]

​     输出: 1

​     示例 2:

  输入: [4,1,2,1,2]

   输出: 4

```c++
int search(int num[],int m) //位运算
{
int res = num[0];
    for (int i = 1; i <m ; ++i) {
        res = res ^ num[i]; //异或运算
    }
    return res;
}
int search1(int num[],int m) //map 统计个数并遍历 当value 为1 返回key
{


   unordered_map<int,int> map;//hash_map c++中  unordered_map 是hash_table 的实现

   for(int i=0;i<m;i++) //遍历统计
   {
       unordered_map<int,int>::const_iterator g = map.find(num[i]);
       if(g==map.end())
       {
           map.insert(make_pair(num[i],1));
       } else
       {
           map.insert(make_pair(num[i],g->second+1));
       }
   }
   unordered_map<int,int>::iterator it;
   for(it = map.begin();it!= map.end();it++ ) //循环map key 为1 return 结果
   {
       if(it->second==1)
       {
           return it->first;
       }
   }
    return 0;
}

int search3(int num[],int m) //暴力求解
{

    for (int i = 0; i <m ; ++i) {
      int flag = 1;
      for(int j=0;j<m;j++)
      {
          if(j==i)
          {
              continue;
          }
          if(num[i]==num[j])
          {
              flag = 0;
              continue;
          }
      }
      if (flag==1)
      {
          return num[i];
      }
}

```





 

 

 

* 题目二：给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

示例:

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9

所以返回 [0, 1]

```c++
int search2(int num[],int m,int  target) //map
{
   unordered_map <int,int> map;
    for (int i = 0; i < m; ++i) {
        unordered_map<int,int>::iterator it;
        it = map.find(target-num[i]);
        if(it!=map.end())
        {
            cout<<it->second<<" "<<i;
        }
     map.insert(make_pair(num[i],i))  ;
    }

    return 0;
}


int search3(int num[],int m,int  target) //暴力求解
{

    for (int i = 0; i <m ; ++i) {
        for (int j = i; j < m ; ++j) {
            if(num[i]+num[j]==target)
            {
                cout<<i<<" "<<j;
                return 0;
            }
        }
    }
   {

   }
}
```



 

 

 

 

* 给定一个整数数组，判断是否存在重复元素。

如果任意一值在数组中出现至少两次，函数返回 true 。如果数组中每个元素都不相同，则返回 false 。

示例 1:

输入: [1,2,3,1]

输出: true

示例 2:

输入: [1,2,3,4]

输出: false

示例 3:

输入: [1,1,1,3,3,4,3,2,4,2]

输出: true

```c++
bool find1(int se[],int n) /*利用map 遍历时查找key是否存在 存在返回true，不存在插入map*/
{
unordered_map<int,int> map;
unordered_map<int,int>::iterator it;
for (int i=0;i<n;i++)
    {
    it = map.find(se[i]);
    if(it!=map.end()) //查找成功
        {
        return true;
        }
    else
        {
        map.insert(make_pair(se[i],1  ));
        }

    }
    return false;
}
```



 

 

* 给定两个字符串 s 和 t，它们只包含小写字母。

字符串 t 由字符串 s 随机重排，然后在随机位置添加一个字母。

请找出在 t 中被添加的字母。

示例 1：

输入：s = "abcd", t = "abcde"

输出："e"

解释：'e' 是那个被添加的字母。

示例 2：

输入：s = "", t = "y"

输出："y"

示例 3：

输入：s = "a", t = "aa"

输出："a"

示例 4：

输入：s = "ae", t = "aea"

输出："a"

提示：

0 <= s.length <= 1000

t.length == s.length + 1

s 和 t 只包含小写字母

```c++
char find_str(string s1,string s2) /*思路 map 统计str1 出现每个字母的个数,str2 将对应的key-1  （map 优化 可以用二维数组 长度26 个字母）*/
{
    if(s1.length()+1!=s2.length()) //长度错误
    {
        return 'A';
    }
    int num [2][26] ={ {0},{0}};  // 自定义map [0][1] a 的次数 [1][1] b  ****
    for(int i=0;i<s1.length();i++)
    {
        num[1][s1[i]-97] +=1; //s1出现 value+1
        num[1][s2[i]-97] -=1; //s2出现 value-1
    }
    num[1][s2[s2.length()-1]-97] -=1; 
    for(int i=0;i<26;i++) // 循环找value = -1 返回对应的字母
    {
        if(num[1][i] == -1)
        {
            return i+97;
            
        }
    }
    return false;
}
```* 题目描述:

  1. 在一个学校办公室，有n个人要做不同的事物处理，已知每个人需要处理的时间为ti，（0<i<=n）,请求出每个人排队等候时间总和最小的时间。

  输入:

  > 用空格隔开的N的整数组成的字符串，表示每人需要处理的时间

  输出:

  > n个人排队时间最小总和

  举例:

  > 输入：5 10 8 7 输出：67

假如 函数的参数数据类型为 vector  利用Algorithm 库中的sort() 排序求解

```c++
int sortedSquares(vector<int>& A) {
    sort(A.begin(),A.end());
    int res = 0, tag = A.size();
    for(int i=0;i<A.size();i++)
    {
        res = res + (tag-i)*A[i];
    }
        return res;
    }
```



* 题目描述:

  1. 开学了，又有好多新生过来报道了。新生晚会需要同学来表演节目，来报名表演节目的人有很多，总共有m个，但是只想从中抽取n个，那么一共有多少种选择方法呢？

  输入:

  > 两个正整数m和n。

  输出:

  > 一个正整数，代表可选择的方法个数。

  举例:

  > 输入(3,2)，输出3。

```
/* 排列组合 从n中挑选m个 
计算公式 ： res = (n!)/(m!(n-m)!)*  代码存在的问题·m！不能超出int_max/

int sortedSquares(int m , int n) {
       int m_ , n_ ,m_n_,d = 1;
       for(int i = 1;i<=m;i++)
       {
           d *=i;
          if(i==n)
          {
              n_ = d;
          }
          if(i==m)
          {
              m_ = d;
          }
          if(i==m-n)
          {
            m_n_ = d;             
          }
       }
       return m_/(n_*(m_n_));
    }

```

* 题目描述:

1. 小明有一个整数序列sequence。小强要在小明的序列中选择两个不同位置，并交换这两个位置上的元素。（被交换的两元素值可能相同）返回小强交换后可能得到的不同序列的个数。

输入:

> 输入一个整型数组sequence。sequence应包括2到47个元素（其中包括2和47），sequence中每个元素的取值范围都在1和47之间（其中包括1和47）

输出:

> 返回可能得到的不同的序列个数

举例:

> sequence:{4, 7, 4},返回3.如果小强交换了第0和1位（从零开始）上的元素，序列将会变为{7, 4, 4}。如果他改而交换第1和2位上的元素，序列将会变为{4, 4, 7}。最后，如果他交换第0和2位上的元素，序列将任然为{4, 7, 4}。这三个结果都是不同的。

```
/* 初始化为res = 1 循环交换*/
   int find ( int sequence[], int n ) {
      int  res = 1;
      for(int i = 0;i<n;i++)
      {
          for(int j = i+1;j<n;j++)
          {
              if(sequence[i] != sequence[j])
              {
                  res++;
              }
          }
      }
        return res;
    }
/* 排列组合思想  先组合 后减去 再加1 */
int find ( int sequence[], int n ) {
       if (n==1)
           return 1;
      int res = 1,d=1,sum = n*(n-1)/2;
      unordered_map<int,int> mp ;
      unordered_map<int,int> ::iterator it;
      mp.insert(make_pair(sequence[0],1));
      for(int i = 1;i<n;i++)
      {

          it = mp.find(sequence[i]);
          if(it!=mp.end())
          {
              it->second +=1;
          }
      }
      d *= n;
      int t = 0;
      for(it = mp.begin();it != mp.end();it++)
      {
            t += it->second*(it->second-1)/2;
      }
        return sum-t+1;
    }
               
                

```

* \1. 给定你一个字符串s。请返回含有连续两个s作为子串的最短字符串。请注意两个s可能会有重叠部分。

  输入:

  > 输入一个字符串s。s含有1到50个字符（其中包括1和50）,s中每个字符都是一个小写字母（从a到z）

  输出:

  > 返回含有连续两个s作为子串的最短字符串

  举例:

  > s = "aba",返回"ababa"。

```c++
bool test(char s[], int s1_s,int s1_e,int s2_s,int s2_e) /* 判断字符串相同函数*/
{
    for(int i=s1_s;i<s1_e;i++)
    {
        if(s[i] != s[s2_s+i] )
        {
            return false;
        }
    }
    return true;
}
char *getShortest(char s[], int len) {
    int length = 0;
    for(int i=1;i<len;i++)
    {
        if(test(s,0,i,len-i,len) == true)
        {length = i;}
    }
    char res[len+len-length] ;
    for(int i = 0;i<len;i++)
    {
        res[i] = s[i];
    }
    for(int i=len+len-length-1,j=len-1;i>=len;i--,j--)
    {
        res[i] = s[j];
    }
    res[len+len-length] = '\0';
    char *p = res ;
    return p;

}
```



