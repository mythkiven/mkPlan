

# 你可能并不了解 #define

### 先聊聊我们知道的`#define`

在预处理器中，实现文本替换；

我们常见的写法：
```
/* 定义常量 */
#define M_PI 3.14159265358979323846264338327950288

/* 定义宏函数 */ 
#define MIN(A,B) ((A) < (B) ? (A) : (B)) 
##注意，上边的MIN这种写法，很常见?？潜藏着不为人知的BUG！！！##

/* 
\ 用于换行
# 用于将宏参数转为字符串*/

#define  TEST(a, b)  \
printf("Test "#a " and " #b"\n")

```

除了常见写法外，还有一些用法写法：
```
/*
## 用于拼接宏参数，实际在可变参数中可能用的多一些
*/

#define MKPRINT(fmt,args...) \
fprintf(stderr, fmt, ##args)

#define MKLOG(fmt, ...)   fprintf(stderr,"MythKiven>> %s\n", [[NSString stringWithFormat:fmt, ##__VA_ARGS__] UTF8String])

...
```

下边切入主题，为什么我说"你可能不了解#define"，因为有些很常见的写法，潜伏着BUG，当BUG出现时，往往不会去在宏找问题。


### "看山是山"

`#define MAX(A,B) ((A) > (B) ? (A) : (B)) ` 一部分同学看到这种写法，可能会觉得太麻烦不如 `#define MAX(A,B)  A > B ? A : B` 来的简洁方便。

可简洁的写法，不一定保险哦：

- 简洁版：
```
#define MAX(A,B)  A > B ? A : B
int a = 3 * MAX(4, 5);
printf("%d",a); // 结果是4惊讶不？  
简单分析下
预处理后：int a = 3 * 4 > 5 ? 4 : 5;
所以 a = 4
```
- 升级版：
```
升级写法：
#define MAX(A,B)  (A > B ? A : B)
int a = MAX(5, 6<7?3:4));
printf("%d",a); // 结果是3,what?？  
简单分析下
预处理后：int a = (5 > 6 < 7 ? 3 : 4 ? 5 : 6 < 7 ? 3 : 4);
哇喔，瞬间感觉脑子不太够用了，最后算出来 a = 3


```
- 终版：
```
也就是现在的写法:
#define MAX(A,B) ((A) > (B) ? (A) : (B)) 

能处理上边的情形了：
int a = 3 * MAX(4, 5);
int b = MAX(5, 6<7?3:4));

在很多的在线教程中，在预处理器-宏参数这一节，都用的这种写法。

但如果是这种情况呢?
float a = 3.0f;
float b = MAX(++a, 3.5f);
printf("a=%f, b=%f",a,b);

```


### "看山不是山"

上一小节介绍了写法 `#define MAX(A,B) ((A) > (B) ? (A) : (B)) ` 是目前在线教程中常见的写法。

```

#define MAX(A,B) ((A) > (B) ? (A) : (B)) 
#define MAXC(A,B) ((A) < (B) ? (B) : (A)) 

float a = 3.0f;
float b = MAX(++a, 3.5f);
printf("b=%f",b); // 5.0

float A = 3.0f;
float B = MAX(A++, 3.5f);
printf("B=%f",B); // 3.5

What??? 已经出了3个版本了，为什么还有意想不到的bug？其实问题的根源在于预编译时，宏的展开。

怎么避免在宏展开时，不改变我们预想的逻辑呢？ 这个问题，我们不是第一次遇到，也不是最后一个。
```

```
//CLANG MIN
#define __NSX_PASTE__(A,B) A##B

#define MIN(A,B) __NSMIN_IMPL__(A,B,__COUNTER__)

#define __NSMIN_IMPL__(A,B,L) ({ __typeof__(A) __NSX_PASTE__(__a,L) = (A); __typeof__(B) __NSX_PASTE__(__b,L) = (B); (__NSX_PASTE__(__a,L) < __NSX_PASTE__(__b,L)) ? __NSX_PASTE__(__a,L) : __NSX_PASTE__(__b,L); })

简化：

 #define MAXX(A,B)    ({ __typeof__(A) __a = (A); __typeof__(B) __b = (B); __a > __b ? __a : __b; })
 ```
 






