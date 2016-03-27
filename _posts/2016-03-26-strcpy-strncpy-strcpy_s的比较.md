---
layout: post
title: strcpy/strncpy/strcpy_s的比较 (Markdown)
tags:
- News
- Tags
- Blog
- Post
---

#strcpy/strncpy/strcpy_s的比较
　　**strcpy**是依据源串的`'/0'`作为结束判断的，不会先检查copy的Buffer的Size，如果目标空间不够，就有BufferOverflow问题。请务必使用按照strncpy_s()、strcpy_s()、strncpy()等不同环境而准备的安全度很高的函数。
　　**strcpy**函数，就象**gets**函数一样，它没有方法来保证有效的缓冲区尺寸，所以它只能假定缓冲足够大来容纳要拷贝的字符串。在程序运行时，这将导致不可预料的行为。
　　另外、即使是使用安全度很高的函数，由于用法方面的原因，也可能发生Buffer Overflow，请注意如下几点。不要在**strcpy_s()、strncpy_s()**进行Buffer Size的误指定。

strcpy_s()两个参数和三个参数的不同用法区别如下代码所示：

```c++
char *str1=NULL;  
str1=new char[20];  
char str[7];  
strcpy_s(str1,20,"hello world");//三个参数  
strcpy_s(str,"hello");//两个参数但如果：char *str=new char[7];会出错：提示不支持两个参数  

```
`第二个参数表示目标缓冲区大小，大于或等于源串的长度+1(存放结束符/0)，strcpy_s必须完全拷贝源串，若只想拷贝一部分源串则应该用strncpy_s函数.`

```c++
char * dst = (char *)malloc(10);  
//strcpy_s(dst, 10, "Hello world!");  
strncpy(dst, "Hello world!", 10);  
```
若使用strncpy不会出错，观察内存可以发现，目标指针所指的缓冲区中完全是从源字符串拷贝过来的字符，没有'/0'结束符，但是若使用strcpy_s则在运行是出现断言出错，所以strncpy仍然不是安全的，因为有可能出现目标指针的字节数不足存放源指针所指向的内容。另外，若使用语句strncpy(dst, "Hello", 10);则会把剩下的空间都填充为空字符/0，带来效率问题，strcpy_s则不会。
所以从VS2005开始已经推出相应的安全版本——strcpy_s（末尾的s可能代表safe）。

> 接口的定义改变如下：

> ```c++
char* strcpy(char* dest, const char* src) --> errno_t strcpy_s(char* dest, size_t numElems, const char* src)
char* strcpy(char* dest, const char* src, size_t count) --> errno_t strcpy_s(char* dest, size_t numElems, const char* src, size_t count)
>```

后者之所以比前者安全，是因为他们在接口增加了一个参数`numElems`来表明dest中的字节数，防止目标指针dest中的空间不够而导致出现Bug，同时返回值改成返回错误代码，而不是为了一些所谓的方便而返回char*。这样接口的定义就比原来安全很多。

####strcpy和strncpy区别：
第一种情况：

```c++
char* p="how are you ?";
char name[20]="ABCDEFGHIJKLMNOPQRS";
strcpy(name,p);   //name改变为"how are you ?\0OPQRS "     ====>错误！
strncpy(name,p,sizeof(name))    //name改变为"how are you ?      "       ====>正确！
```

第二种情况：

```c++
char* p="how are you ?";
char name[20];
strcpy(name,p);    //name改变为"how are you ?\0未知字符 "     ====>错误！
name[sizeof(name)-1]='"0'      //和上一步组合，得到正确的结果！
strncpy(name,p,sizeof(name));        //name改变为"how are you ?      "      ====>正确！
```

第三种情况：

```c++
char* p="how are you ?";
char name[10];
strcpy(name,p);      //name改变为"how are yo"     ====>无结束符'\0'，错误！
name[sizeof(name)-1]='"0'      //和上一步组合，弥补结果。但要注意，字符传递错误！
strncpy(name,p,sizeof(name));      //和单纯的一步strcpy结果一样！
```

 


####总结：
> **strcpy**
> - 如果源长>目标长，则将源长中等于目标长的字符拷贝到目标字符串
> - 如果源长<目标长，则源长全部拷贝到目标字符串，不包括'\0'
> **strncpy**
> - 如果目标长>指定长>源长，则将源长全部拷贝到目标长，自动加上'\0'
> - 如果指定长<源长，则将源长中按指定长度拷贝到目标字符串，不包括'\0'
> - 如果指定长>目标长，error happen!

####补充：
#####memcpy strcpy strncpy lstrcpy lstrncpy wstrcpy, memmove
**memcpy**
>原型:
extern void *memcpy(void *dest, void *src, unsigned int count);
功能：由src所指内存区域复制count个字节到dest所指内存区域。
说明：src和dest所指内存区域不能重叠，函数返回指向dest的指针。
出错可能：
1,dest buffer 不够大，不能容纳src的内容。
2,count 大于 src buffer长度， 造成拷贝越界到别的内容。
3,dest 和 src 有内存重叠。
例如，array[] = {0,1,2,3,4}
memcpy(&array[2], &array[0], 3);


**strcpy**
>原型：extern char *strcpy(char *dest,char *src);
功能：把src所指由'\0'结束的字符串复制到dest所指的数组中。
说明：src和dest所指内存区域不可以重叠且,dest必须有足够的空间来容纳src的字符串。返回指向dest的指针。
出错可能：
1,src 和 dest有重叠
2,dest空间不足以来容纳src的字符串
3,用之前为初始化数组
char* p="I love pizza!";
char name[20]="ABCDEFGHIJKLMNOPQRS";
strcpy(name,p); //name变为"I love pizza! OPQRS " 错！
4,字符串中有中文字符，中文字符占两个字节。
char* p = "ABCDEFGHIJKLMNOPQR啊";
strcpy(name, p);


**strncpy**
>原型：extern char *strncpy(char *dest, char *src, int n);
功能：把src所指由'\0'结束的字符串的前n个字节复制到dest所指的数组中。
说明：
        如果src的前n个字节不含NULL字符，则结果不会以NULL字符结束。
        如果src的长度小于n个字节，则以NULL填充dest直到复制完n个字节。
        src和dest所指内存区域不可以重叠且dest必须有足够的空间来容纳src的字符串。
        返回指向dest的指针。
出错可能：
1,src 和 dest 重叠
2,dest 不足以容纳 src的字符串
3,安全性高于strcpy,但是当dest长度大于src长度，两者出同样的错误。字符串截断，没有\0.


**lstrcpy lstrncpy**
>原型: LPTSTR lstrcpy(LPTSTR dest, LPTSTR src);
      LPTSTR lstrcpy(LPTSTR dest, LPTSTR src, int count);
功能：类似于strcpy 和strncpy
说明：这是windows API,只能用于windows平台。和标准C函数strcpy,strncpy区别在于，如果定义了unicode，lstrcpy() 就变成 wstrcpy()完成double-byte unicode字符的拷贝。


**wstrcpy**
>原型：wchar_t *wstrcpy (wchar_t *ws1,wchar_t *ws2 );
     wchar_t *wstrncpy(wchar_t *ws1,wchar_t *ws2,int n );
功能：类似于strcpy 和strncpy,但是作用于宽字符，
说明：所在头文件是<wstring.h>,而不是<string.h>
出错可能：
原理类似窄符拷贝


**memmove**
>原型：extern void *memmove(void *dest, const void *src, unsigned int count);
功能：由src所指内存区域复制count个字节到dest所指内存区域。
说明：src和dest所指内存区域可以重叠，但复制后src内容会被更改。函数返回指向dest的指针。由此可见，用memmove 比memcpy安全的多！

