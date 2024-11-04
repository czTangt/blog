# Some About Python


这里是 python 使用技巧的记录，包括日常使用和数据之间的转换。

&lt;!--more--&gt; 

## 基础知识

### 字符串

`&#34;&#34;&#34; &#34;&#34;&#34;` 可以存储数行字符串

```python
str = &#34;&#34;&#34;learn python the smart way 2nd edition
hello word&#34;&#34;&#34;
```

使用 `enumerate()` 可以获得元素的序号

```python
for idx, c in enumerate(str):
    print(idx, c)
```

`str.split` 会把字符串划分为一个列表，依照空格进行划分

```python
for word in str.split():
    print(word)
```

`str.splitlines` 会把字符串划分为一个列表，依照&#34;\n&#34;进行划分

```python
for line in str.splitlines():
    if(line.startswith(&#34;hello&#34;)): # startswith 
    	print(line)
```

### 函数

接收不定长参数，`*args` 表示参数数目不定，可以看成一个元组，把第一个参数后面的参数当作元组中的元素

```python
def add(x, *args):
    total = x
    for arg in args:
        total &#43;= arg
    return total
```
上面的函数不能使用关键词传入参数，要使用关键词 `**kwargs`，它表示参数数目不定，相当于一个字典，键和值对应于键值对

```python
def add(x, **kwargs):
    total = x
    for arg, value in kwargs.items():
        print(&#34;adding %s=%s&#34; % (arg,value))
        total &#43;= value
    return total

# 使用方法如下：
def foo(*args, **kwargs):
    print(args, kwargs)
add(1, 2, 3, 4)
foo(2, 3, x=&#39;bar&#39;, z=10)
```

`map` 方法生成序列，`map(aFun, aSeq)`，函数 aFun 应用到序列 aSeq 上的每一个元素上，返回一个列表，不管这个序列原来是什么类型。事实上，根据函数参数的多少，map 可以接受多组序列，将其对应的元素作为参数传入函数。

```python
def square(a, b, c):
    return a**2 &#43; b &#43; c
a = [1,2,3]
b = (4, 5, 6)
print(list(map(square, a, b, b)))
```

### 文件读写

python 提供安全的 `with` 来进行文件读写，当 with 块的内容结束后，Python 会自动调用它的 `close` 方法，确保读写的安全。

| 模式  |   描述                                                    |
| ---- | ----------------------------------------------------------- |
| `r`&amp;emsp;  | **只读**。该文件必须已存在。                                 |
| `r&#43;` | **可读可写**。该文件必须已存在，写为追加在文件内容末尾。     |
| `rb` | 表示以二进制方式读取文件。该文件必须已存在。                 |
| `w`  | **只写**。打开即默认创建一个新文件，如果文件已存在，则覆盖写（即文件内原始数据会被新写入的数据清空覆盖）。 |
| `w&#43;` | **写读**。打开创建新文件并写入数据，如果文件已存在，则覆盖写。 |
| `wb` | 表示以二进制写方式打开，只能写文件， 如果文件不存在，创建该文件；如果文件已存在，则覆盖写。 |
| `a`  | **追加写**。若打开的是已有文件则直接对已有文件操作，若打开文件不存在则创建新文件，只能执行写（追加在后面），不能读。 |
| `a&#43;` | **追加读写**。打开文件方式与写入方式和`a`一样，但是可以读。需注意的是你若刚用`a&#43;`打开一个文件，一般不能直接读取，因为此时光标已经是文件末尾，除非你把光标移动到初始位置或任意非末尾的位置。 |

```python
import os
os.remove(&#39;newfile.txt&#39;)
with open(&#39;newfile.txt&#39;,&#39;w&#43;&#39;) as f:
    for i in range(30):
        x = 1.0 / (i - 10)
        f.write(&#39;hello world: &#39; &#43; str(i) &#43; &#39;\n&#39;)
```

### 其他

通过 `split` 对 &#34;,&#34; 进行分割，使得一行可以输入多个值。

```python
a, b = input().split(&#34;,&#34;)
print(f&#34;a = {a}, b = {b}&#34;)
```

`print` 操作，默认每次输入后会换行，控制结尾的参数是 end，设置 end 把 &#34;\n&#34; 替换成了 &#34;//&#34;。同时它一次也可以输出多个内容，默认以空格分隔，这里控制分割的参数就是 sep，修改之后空格变成 &#34;//&#34;。

```python
print(&#34;data&#34;, end=&#34;//&#34;)
print(&#34;Data&#34;, &#34;whale&#34;, sep=&#34;//&#34;)
```

## 数据转换

这里强制自己使用byte类型，这样可以统一python的不同数据类型

### AllToBytes

字符串转化为 `bytes`，也可以直接在前面加 `&#39;b&#39;` 来转换

```python
string = &#34;Hello World&#34;
str_byte = bytes(string, &#39;utf-8&#39;)  # -&gt; b&#39;Hello World&#39;
```

二进制字符串转化为 `bytes`

```python
hex_string = &#34;68 656c6c6f20776f726c64&#34;  # 这里空格不会影响结果，但是需要是两个字符(68中间不能加空格)一组
hex_byte = bytes.fromhex(hex_string)  # -&gt; b&#39;hello world&#39;
# list(hex_byte) -&gt; [104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100]
```

长整型转换为 `bytes`，小端序方式

```python
long_i = 6788912312  # 下面就是计算转化为16进制的字节数
int.to_bytes(long_i, (long_i.bit_length() &#43; 7) // 8, byteorder=&#34;little&#34;)  # -&gt; b&#39;\xb8\x94\xa6\x94\x01&#39;
```

十六进制数转化为 `bytes`，小端序方式

```python
hex_int = 0x12345678
int_byte = int.to_bytes(hex_int, 4, byteorder=&#39;little&#39;)  # -&gt; b&#39;xV4\x12&#39;
```

整型列表转化为 `bytes`

```python
list_num = [0x12, 0x34, 0x56, 0x78]
list_byte = bytes(list_num)  # -&gt; b&#39;\x124Vx&#39;
```

字节列表转化为 `bytes`，先转化为字符串，再转化

```python
str_list = [&#39;1&#39;, &#39;C&#39;, &#39;E&#39;, &#39;B&#39;, &#39;E&#39;, &#39;0&#39;, &#39;8&#39;, &#39;9&#39;, &#39;7&#39;, &#39;4&#39;, &#39;A&#39;, &#39;9&#39;, &#39;6&#39;, &#39;1&#39;, &#39;C&#39;, &#39;5&#39;]
# 先转str再转byte
bytes(&#34;&#34;.join(str_list), encoding=&#34;utf-8&#34;)  # -&gt; b&#39;1CEBE08974A961C5&#39;
```

### AllToBytes

bytes 转化为字符串

```python
byte = b&#39;Hello World&#39;
byte_str = str(byte, &#39;utf-8&#39;)  # -&gt; &#39;Hello World&#39;
```

bytes 转化为十六进制字符串

```python
byte = b&#39;hello world&#39;
byte_hex = byte.hex()  # -&gt; &#39;68656c6c6f20776f726c64&#39;
```

bytes 转化为长整型，小端序

```python
byte = b&#39;\xb8\x94\xa6\x94\x01&#39;
i = int.from_bytes(byte, byteorder=&#39;little&#39;)  # -&gt; 6788912312
```

bytes 转化为十六进制整型

```python
byte = b&#39;xV4\x12&#39;
int_num = int.from_bytes(byte, byteorder=&#39;little&#39;)  # -&gt; 305419896 0x12345678
```

bytes 转化为整型列表

```python
byte = b&#39;\x124Vx&#39;
list_num = list(byte)  # -&gt; [18, 52, 86, 120]
```

bytes 转化为字符串列表

```python
byte = b&#39;1CEBE08974A961C5&#39;
str_list = [str(byte[i:i &#43; 2], &#39;utf-8&#39;) for i in
            range(0, len(byte), 2)]  # -&gt; [&#39;1C&#39;, &#39;EB&#39;, &#39;E0&#39;, &#39;89&#39;, &#39;74&#39;, &#39;A9&#39;, &#39;61&#39;, &#39;C5&#39;]
```

### 其余转换处理

转化为字符串

```python
chr(a) 			    # 将 int 类型的 a 根据其 ascii 码转化为 str 字符
hex(a)				# 将 int 类型的 a 转化为其十六进制 str 类型
str(a)				# 将所有类型的 a 按照其本身转化为 str 类型
str = a.decode()	# 将 bytes 类型的 a 转化为 str 类型
```

转化为整型

```python
# a 为 k 进制数，使用 int 将 k 进制数的 a 转化为十进制数
# # int(a) 直接将字符 a 转化为 int 类型，此时 a 必须为数字字符，注意：不是转化 为ascii 码，而是转化为数字类型，即值不变，类型改变
int(a,k) 
# 将 str 类型的十六进制数 a 转化为 int 类型(这里十六进制需要加上0x)
eval(a) 
# 将字符类型的 a 按其 ascii 码转化为 int 类型
ord(a)
```
## 数据处理
`struct` 模块可以解决 `bytes` 和其他二进制数据类型的转换。`pack` 函数把任意数据类型变成 `bytes`，`unpack` 把 `bytes` 变成相应的数据类型。这里的格式就是(format:str, *v1*, *v2*, *…*)，其中format对于后面的数据进行匹配，然后输出。

### pack
`pack` 函数把任意数据类型变成 `bytes`
```python
struct.pack(&#39;&lt;II&#39;, 10240099, 1767863401)  # -&gt; b&#39;\x00\x9c@ci_ti&#39; 如果符合ascii的标准，就直接转化为字符
```

### unpack
`unpack` 把 `bytes` 变成相应的数据类型
```python
struct.unpack(&#39;&lt;I&#39;, b&#39;it_i&#39;)  # -&gt; (1767863401,) 这里只有一个符合 I 的规则，所以只有一个数据
```

### 数据格式
`format` 参数就是上面使用的描述符，`struct` 利用它可以指定使用大端序还是小端序来解析或者生成数据

| Character | Byte order           | Size     | Alignment           |
| --------- | -------------------- | -------- | ------------------- |
| `@`         | native               | native   | native，凑足4个字节 |
| `=`         | native               | standard | none                |
| `&lt;`         | little-endian        | standard | none                |
| `&gt;`         | big-endian           | standard | none                |
| `!`         | network(=big-endian) | standard | none                |

数据格式，用于匹配当前字符的数据

| Format | C Type             | Python type        | Standard size |
| ------ | ------------------ | ------------------ | ------------- |
| `x`      | pad byte           | no value           |               |
| `c`      | char               | string of length 1 | 1             |
| `b`      | signed char        | integer            | 1             |
| `B`      | unsigned char      | integer            | 1             |
| `?`      | _Bool              | bool               | 1             |
| `h`      | short              | integer            | 2             |
| `H`      | unsigned short     | integer            | 2             |
| `i`      | int                | integer            | 4             |
| `I`      | unsigned int       | integer            | 4             |
| `l`      | long               | integer            | 4             |
| `L`      | unsigned long      | integer            | 4             |
| `q`      | long long          | integer            | 8             |
| `Q`      | unsigned long long | integer            | 8             |
| `f`      | float              | float              | 4             |
| `d`      | double             | float              | 8             |
| `s`      | char[]             | string             | 1             |
| `p`      | char[]             | string             | 1             |
| `P`      | void *             | integer            | 0             |

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: http://localhost:1313/blog/categories/programming/python/  

