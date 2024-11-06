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
 命名空间增加了标识符的使用率，减少因为命名产生的冲突。对于命名空间而言，其中的变量和函数等都是属于自己这个空间的，需要通过标识空间名来指明数据的归属，这样可以使得不同命名空间可以存在同样名称的数据，它们之间不会产生冲突。
 - 声明命名空间：`namespace 空间名{}`，命名空间的声明不能写在函数中
 - 访问数据：`空间名::空间中的成员名`
 - 省略前缀的方式：`using namespace 空间名`

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

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/programming/cpp/  

