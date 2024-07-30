# 正则表达式
## 语法基础

#### 特殊字符
特殊字符在正则表达式中有特殊的含义。

|符号|意义|
|---|---|
|*|代表0个或多个|
|?|代表0个或1个|
|+|代表1个或多个|
|.|代表除了换行符以外的所有字符。|
|\d|匹配数字，效果同[0-9]|
|\D| 匹配非数字字符，效果同[^0-9]|
|\w|匹配数字，字母和下划线|
|\W|匹配非数字，字母和下划线|
|\s|匹配任意的空白符（空格，换行，回车，换页，制表符）效果同[ \f\n\t\t]|
|\S|匹配任意的非空白符|


#### []的使用
[] 匹配方括号中的任意一个字符，中间若加-符号则表示范围

例子：[aeiou]、[A-Z]、[0-9]、[a-zA-Z0-9]  

如果我们在左中括号的右边加一个^符号，就意味着除了这些内容，都能进行匹配。
[^wang] ^托字符在方括号中，表示匹配除了wang这几个字母外的所有字符 

#### {}的使用
大括号表示大括号前面的字符可以出现的次数。

|符号|意义|
|---|---|
|{n}|代表前面的字符只能出现n次，不能多不能少。|
|{n,}|代表前面的字符至少出现n次，只能多不能少。|
|{,n}|代表前面的字符最多出现n次，不能多只能少。|
|{m,n}|代表前面的字符最少出现m次，最多出现n次|

例子如下：
```python
print(re.findall("ab{2,6}","abbb"))  # ['abbb'] (一个a,2~6个b)
```

    ['w']

## 语法进阶
#### 贪婪与懒惰

当正则表达式中包含能接受重复的限定符时，通常的行为是（在使整个表达式能得到匹配的前提下）匹配尽可能多的字符。以这个表达式为例：`a.*b`，它将会匹配最长的以 a 开始，以 b 结束的字符串。如果用它来搜索 `aabab` 的话，它会匹配整个字符串 `aabab`。这被称为**贪婪匹配**。

有时，我们更需要**懒惰匹配**，也就是**匹配尽可能少的字符**。前面给出的限定符都可以被转化为**懒惰匹配模式**，只要在它**后面加上一个问号?**。这样 `.*?` 就意味着匹配任意数量的重复，但是在能使整个匹配成功的前提下使用最少的重复。现在看看懒惰版的例子吧：

	a.*?b
	
匹配最短的，以a开始，以b结束的字符串。如果把它应用于 `aabab` 的话，它会匹配 `aab`（第一到第三个字符）和 `ab`（第四到第五个字符）。

**表5.懒惰限定符**

代码/语法 | 说明
---- | ----
*? | 重复任意次，但尽可能少重复
+? | 重复1次或更多次，但尽可能少重复
?? | 重复0次或1次，但尽可能少重复
{n,m}? | 重复n到m次，但尽可能少重复
{n,}? | 重复n次以上，但尽可能少重复

```
import re
print(re.findall(r'\d+','12345')) # ['12345']
print(re.findall(r'\d+?','12345'))#非贪婪模式 ['1', '2', '3', '4', '5']
```


## 测试链接文档
.setting.md

## 锚字符（边界字符）
* ^ 行首匹配，和在[]里的^不是一个意思  
* $ 行尾匹配  
* \A 匹配字符串开始，它和^的区别是\A只匹配整个字符串的开头，即使在re.M(多行)模式下也不匹配其他行行首
* \Z 匹配字符串结束，只匹配整个字符串的结束  
* \b 匹配一个单词的边界，即单词和空格间的位置，一般用r"\b*",因为\b本身是转义字符
* \B 匹配非单词边界


```python
import re
re.findall("[0-35-9]","1234567890")
```




    ['1', '2', '3', '5', '6', '7', '8', '9', '0']



## 匹配多个字符  
* (xyz) 匹配小括号内的xyz（作为一个整体去匹配）
* ?:x 类似(xyz)，但不表示一个组
 * 括号在findall中表示一个分组，并提取匹配到的字串，括号可以嵌套，
 * 如果匹配到，结果返回1个列表，列表元素由1到多个元组组成，表示匹配到的1到多个结果对象
 * 每个元组第一个元素表示最外层括号组，第二个元素为内层括号组，依次类推
 * 多层嵌套元组结果先从外到内，再从左到右，类似深度遍历
 * 括号在match和search中返回的分组用返回对象的group或groups方法得到所需字符串

* x|y  匹配x或者y



```python
import re
strs = "wang is a good man! wang is a nice man! wang is a very handsome man!"
print(re.findall(r"^wang.*man", strs))
print(re.findall(r"wang.*?man", strs))
print(re.findall(r"((w(.n)g).*?(m(.)n))", strs))
```

    ['wang is a good man! wang is a nice man! wang is a very handsome man']
    ['wang is a good man', 'wang is a nice man', 'wang is a very handsome man']
    [('wang is a good man', 'wang', 'an', 'man', 'a'), ('wang is a nice man', 'wang', 'an', 'man', 'a'), ('wang is a very handsome man', 'wang', 'an', 'man', 'a')]
    


```python
import re
strs = r"/* part1 */  /* part2 /"
# pattern字符串前即使加了r有时也要加入\转义，因为*在正则中表示前面字符的0到多个，所以需要转义
print(re.findall(r"/\*.*\*/", strs))
```

    ['/* part1 */']
    


```python
import re
strs = '010-41293905'
m = re.match(r"(?P<first>\d{3})-(?P<last>\d{8})", strs)
# 返回匹配结果
print(m.group())
# 返回匹配结果
print(m.group(0))
# 返回第一个子分组
print(m.group(1))
# 返回第二个子分组
print(m.group(2))
# 返回所有子分组
print(m.groups())
# 分组可以指定名称
print(m.group("first"))
```

    010-41293905
    010-41293905
    010
    41293905
    ('010', '41293905')
    010
    


```python
import re
strs = '010-41293905'
m = re.match(r"(0(1)0)-(\d{8})", strs)
print(m.group())
print(m.groups())
print(m.group(1))
print(m.group(2))
```

    010-41293905
    ('010', '1', '41293905')
    010
    1
    