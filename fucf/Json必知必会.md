[TOC]
# JSON必知必会
Author：fucf

## 第二章 JSON语法
JSON中使用冒号(:)来分隔名称和值。名称始终在左侧，值始终在右侧。
```
"animal":"horse"
"animal":"dog"
```

### 正确的JSON语法
* 字面量：指字面意思与其想要表达的意思是完全一致的值。
* 变量：通过形如`x`的标识符来表示的、可以修改的一类值。
* 名称需要始终被双引号包裹。双引号中的名称可以是任何有效的字符串。你甚至可以在名称中使用单引号（不推荐）
* JSON中的名称-值对是一种对许多系统都十分友好的数据结构，而使用空格和特殊字符（即a~z、0-9除外的其他字符）会忽略可移植性。
* 在JSON中，对于名称：两边必须加上双引号。而对于值：当值是字符串时，必须使用双引号。而在JSON中，还有数字、布尔值、数组、对象、null等其他数据类型，这些都不应被双引号包裹。
* 机器读取JSON数据的指令：
    * { (左花括号)指“开始读取对象”
    * } (右花括号)指“结束读取对象”
    * [ (左方括号)指“开始读取数组”
    * ] (右方括号)指“结束读取数组”
    * : (冒号)指“在名称-值对中分隔名称和值”
    * , (逗号)指“分隔对象中的名称-值对”或者“分隔数组中的值”；也可以认为是“一个新部分的开始”
    * JSON中**名称**两边必须加上双引号（这点和Javascript不同）
 
 在工作中使用JSON，很重要的一点就是验证。
    
 JSON文件后缀`.JSON`
 
 JSON的媒体类型`application/JSON`
 
 
## 第三章 JSON的数据类型
JSON中的数据类型：
* 对象
* 字符串
* 数字
* 布尔值
* null
* 数组

### JSON中的对象类型
对象类型是使用逗号分隔的名称-值对构成的集合，并使用花括号（{}）包裹。

### JSON中的字符串类型
一个字符串值，如"my string".**字符串的两边必须被双引号包裹。**

```JSON
// 这不是一个合法的JSON
{
    'title': 'This is not valid JSON'
}
```


### JSON中的数字类型
一个数字值，如42. JSON中的数字可以是整数、小数、负数或者指数。

### JSON中的布尔类型
在JSON中，布尔值仅使用小写形式：true或false。任何其他形式的写法都会报错。

### JSON中的null类型
JSON中null必须使用小写形式。

### JSON中的数组类型
JSON中，数组中混合使用数据类型的情况是合法的。但是最好不要这样做。因为很多类型的语言是不支持混合使用数据类型。
如果将JSON数据传递给一个不使用Javascript的系统，那么在解析时很可能会出错。

### 对象和数组的区别
* 对象是名称-值对构成的列表或集合。数组是值构成的列表或集合。
* 对象和数组另一个关键的区别是，数组中所有的值应具有相同的数据类型。

## 说明
由于下面几章内容和工作目前关系不大，不予阅读。






