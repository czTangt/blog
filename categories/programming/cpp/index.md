# C 拾遗和 C&#43;&#43; 学习


学习 C&#43;&#43; 的使用，主要区分 C&#43;&#43; 和 C 的区别。

&lt;!--more--&gt; 

 ## 头文件
C&#43;&#43; 包含标准 C 语言头文件，对于原本 C 的头文件，C&#43;&#43; 有两种方式进行引用，一种是原有的方式（后面跟 `.h`），一种就是去掉 `.h`，在库前面添加一个 `c` 标识这是原本 C 的头文件。对于自己写的头文件还是原方式进行引用，即 `&#34;&#34;`。
```c&#43;&#43;
#include &lt;iostream&gt; // 基本输入输出
#include &lt;cstdio&gt;   // 在原来 C 语言的库前面加一个 c，去掉 .h
#include &lt;stdio.h&gt;  // 采用原有方式进行引用
#include &#34;myFile.h&#34; // 自己的文件，采用原有方式引用
```

 ## 命名空间
 ### 基础知识
 命名空间增加了标识符的使用率，减少因为命名产生的冲突。对于命名空间而言，其中的变量和函数等都是属于自己这个空间的，需要通过标识空间名来指明数据的归属，这样可以使得不同命名空间可以存在同样名称的数据，它们之间不会产生冲突。
 - 声明命名空间：`namespace 空间名{}`，命名空间的声明不能写在函数中
 - 访问数据：`空间名::空间中的成员名`
 - 省略前缀的方式：`using namespace 空间名`，表示从这个地方开始，后面都可以省略前缀。

```c&#43;&#43;
#include &lt;iostream&gt;
using namespace std; // 标准命名空间

namespace A{
    int num = 1;
    void print(){
        printf(&#34;A\n&#34;);
    }
}
namespace B{
    int num = 2;
    void print(){
        printf(&#34;A\n&#34;);
    }
}
namespace C{
    namespace D{
        int cd_num = 3;
    }
}

int g_num = 1001;
int main(){
    // 使用省略前缀的方式，可以直接使用其中的函数
    cout &lt;&lt; &#34;命名空间&#34; &lt;&lt; endl;     
    std::cout&lt;&lt; &#34;命名空间&#34; &lt;&lt; endl;

    // 不同命名空间访问数据
    A::num = 2;
    B::print();

    // 省略前缀方式访问数据
    using namespace A;
    num = 3;    // A 命名空间数据
    using namespace B;
    B::num = 4; // B 命名空间数据，省略前缀需要注意二义性的问题，所以还需要标识命名空间

    // 嵌套命名空间
    C::D::cd_num = 5;
    using namespace C::D;
    cd_num = 5;

    // :: 为作用域分辨符，同时可以用于指明全局变量
    int g_num = 11;
    printf(&#34;num %d\n&#34;, g_num);      // 变量访问采用就近原则，这里就是访问上面的局部变量，返回 11
    printf(&#34;num %d\n&#34;, ::g_num);    // 使用作用域分辨符，指明访问全局变量，返回 1001
    return 0;
}
```

### 内联命名空间
对于嵌套命名空间，访问需要使用多层空间名，但是可以采用内联命名空间的方式直接进行访问。
```c&#43;&#43;
#include &lt;iostream&gt;
using namespace std;
namespace Version{
    inline namespace v2017{
        void showVersion(){
            cout &lt;&lt; &#34;v2017&#34; &lt;&lt; endl;
        }
    }

    namespace v2020{
        void showVersion(){
            cout &lt;&lt; &#34;v2020&#34; &lt;&lt; endl;
        }
    }
}

int main(){
    // 上面采用内联命名空间，即添加了 inline，设置了默认的情况，使得下面的两个语句效果等价。
    // Version::v2017::showVersion();
    Version::showVersion();
}
```

## 动态内存分配
C 的动态内存采用函数 `malloc calloc realloc free`，具体可以从 [堆基础](https://www.uf4te.cn/posts/463ab4ed.html) 获取详细介绍。而 C&#43;&#43; 使用 `new delete` 操作符进行动态内存分配。

```c&#43;&#43;
#include &lt;iostream&gt;
using namespace std;

void showArr(int* arr, int len){
    for (int i = 0; i &lt; len; i &#43;&#43;){
        cout &lt;&lt; arr[i] &lt;&lt; &#34; &#34;;
    }
    cout &lt;&lt; endl;
}
int main(){
    // C 内存分配
    int *p = (int*)calloc(5, sizeof(int));
    showArr(p, 5);       // calloc 会初始化为 0，所以可以直接打印
    free(p);
    p = nullptr; // free 只是清除空间，还需要制空指针，不然就是一个野指针

    // C&#43;&#43; 内存分配
    int* page = new int; // 申请一个 int
    *page = 19;          // 这里简单 new 不会初始化为 0，需要自行设置值
    // int* page = new int(19); // 可以使用 c&#43;&#43; 的括号赋值直接进行赋值
    cout &lt;&lt; *page &lt;&lt; endl;
    delete page;
    page = nullptr;

    page = new int[29];  // 申请一个数组，这里也不会初始化，所以可以采用下面的括号赋值，后面自动初始化为 0；或者使用 for 循环进行赋值
    // page = new int[29]{1, 2, 3, 7, 12, 12};
    showArr(page, 29);
    delete[] page;
    page = nullptr;

    return 0;
}
```

## 枚举类型
C 和 C&#43;&#43; 都提供了枚举类型，两者有一定的区别。这里主要就是 C&#43;&#43; 的枚举类型，不涉及 C 的。

### enum 枚举类型
C&#43;&#43; 中的 enum 就是枚举类型的标识符，它只允许赋值枚举值；同时枚举元素会暴露在外部作用域，两个不同枚举类型若是含有相同枚举元素，会产生冲突；不同的枚举可以直接进行比较
```c&#43;&#43;
// 定义
enum WEEK {MON, TUE, WED, THI, FIR, SAT, SUN};
enum SHAPE {CIRCLE, RECT, POINT, LINE};

// 只允许赋值枚举值，前面的 enum 不进行添加就可以使用
WEEK today = 3;    // 错误 error C2440：“初始化”：无法从“int”转换为“main::WEEK”
today = CIRCLE;         // 错误 error C2440：“=”：无法从“main::SHAPE”转换为“main::WEEK”

// 枚举元素暴露在外部作用域
enumc OTHER {RECT};     // 错误 error C2365：“RECT”：重定义；以前的定义是“枚举数”
Int RECT = 12;          // 错误同上，但是可以通过枚举名访问指定的枚举属性
OTHER::RECT;            // 正确

// 不同类型的枚举也可以直接比较
if (CIRCLE == MON){
    cout &lt;&lt; &#34;yes&#34; &lt;&lt; endl;
}
```

### enum class 强枚举类型
这里强枚举类型不会将枚举元素暴露在外部作用域，必须通过枚举名去访问；同时不相关的两个枚举类型不能直接比较，编译报错

```c&#43;&#43;
// 定义
enum class WEEK {MON, TUE, WED, THI, FIR, SAT, SUN};
enum class SHAPE {CIRCLE, RECT, POINT, LINE};

// 不暴露在外部作用域
cout &lt;&lt; SHAPCE::RECT &lt;&lt; endl;   // 输出 1

// 不相关的两个枚举类型不能直接比较
if (SHAPCE::RECT == WEEK::MON){ // error c2676：二进制“==&#34;：“main::SHAPE&#34;不定义该运算符或到预定义运算符可接收的类型的转换
    cout &lt;&lt; &#34;yes&#34; &lt;&lt; endl;
}
```

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/programming/cpp/  

