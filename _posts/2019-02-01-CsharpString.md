---
layout: mypost
title: C#之String类的常用方法
tags: C#
---

### 	String.Compare( A, B[, bool ignoreCase ])
两个String对象逐一字符对比，如果完全相等则返回0，A总体的排列顺序优先于B，则返回-1，否则返回1.最后一个可选参数ignoreCase，但它为true的时候，不区分大小写。
### String.Concat(A, B, params string[] args)
连接字符串
### A.Contains(B)
A是否包含B，返回True或False
### String.Copy(A)
复制A字符串
### A.CopyTo( int A_Index, char[] B, int B_Index, int count)
字符串A，从A_Index开始截取count长度的字符，覆盖B中从B_Index开始相同长度的字符。
```
String a = "abcd";
char[] b = { 'e','f','g','h','i' };
a.CopyTo(2, b, 2, 2);
Console.WriteLine(b);   // 输出 e f c d i
```
### A.EndsWith(B)
判断A的结尾是否匹配B，返回True或False
### A.Equals(B) 等同于 String.Equals(A, B)
判断A是否等于B，返回True或False
### String.Format()
主要用来格式化时间
```
DateTime dt = new DateTime(2017,4,1,13,16,32,108);
string.Format("{0:y yy yyy yyyy}",dt); //17 17 2017 2017
string.Format("{0:M MM MMM MMMM}", dt);//4  04 四月 四月
string.Format("{0:d dd ddd dddd}", dt);//1  01 周六 星期六
string.Format("{0:t tt}", dt);//下 下午
string.Format("{0:H HH}", dt);//13 13
string.Format("{0:h hh}", dt);//1  01
string.Format("{0:m mm}", dt);//16 16
string.Format("{0:s ss}", dt);//32 32
string.Format("{0:F FF FFF FFFF FFFFF FFFFFF FFFFFFF}", dt);//1 1  108 108  108   108    108
string.Format("{0:f ff fff ffff fffff ffffff fffffff}", dt);//1 10 108 1080 10800 108000 1080000
string.Format("{0:z zz zzz}", dt);//+8 +08 +08:00
string.Format("{0:yyyy/MM/dd HH:mm:ss.fff}",dt);　　//2017/04/01 13:16:32.108
string.Format("{0:yyyy/MM/dd dddd}", dt);　　　　　　//2017/04/01 星期六
string.Format("{0:yyyy/MM/dd dddd tt hh:mm}", dt); //2017/04/01 星期六 下午 01:16
string.Format("{0:yyyyMMdd}", dt);　　　　　　　　　//20170401
string.Format("{0:yyyy-MM-dd HH:mm:ss.fff}", dt);　//2017-04-01 13:16:32.108
```
### A.IndexOf(value)
返回value在A中第一次出现的索引,char可以是String也可以是char
### A.IndexOf(value, int startIndex)
跟上面方法一样，区别就是上面从索引0开始，这个方法从startIndex开始查找
### A.IndexOfAny(char[] anyOf)
返回anyOf中任一字符第一次出现在A中索引
### A.Insert(int startIndex, B)
从A的索引startIndex开始插入B
### String.IsNullOrEmpty(A)
判断A是否为空字符串
### String.Join(string separator, string[] value)
把一个string数组拼成一个字符串，中间用分隔符separator
### String.Join( string separator, string[] value, int startIndex, int count )
跟上面方法一样，区别就是指定了索引和长度
### A.LastIndexOf(B)
B最后一次出现在A里面的索引，B可以是string或char
### A.Remove(int startIndex)
删除A字符串从索引startIndex到结尾
### A.Replace(old, new)
将A字符串中的old字符替换成new字符，old/new可以是string或char
### A.split(params char[] separator)
分割字符串A，返回一个字符串数组
### A.StartWith(B)  
判断A的开头是否匹配B，返回True或False
### A.ToCharArray()
把字符串变成一个char数组
### A.ToCharArray(int startIndex, int length)
把字符串变成一个char数组,指定了起始索引和长度
### A.ToLower()
A字符串所有字符全部转换为小写
### A.ToUpper()
A字符串所有字符全部转换为小写
### A.Trim()
移除A首尾的空白字符
