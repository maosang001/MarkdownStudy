# Markdown教程

## <mark>简述</mark>
* 该笔记参考https://www.runoob.com/markdown/md-tutorial.html
* Markdown编写的文档可以导出Html、Word、图像、pdf等多种格式的文档
* Markdown编写的文档后缀为.md或.markdown

***
## <mark>Markdown标题</mark>
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
***
## <mark>Markdown段落格式</mark>
Markdown段落没有特殊的格式，直接编写文字就好了。段落换行是使用不少于两个空格，段落结束是使用回车

段落第1行(末尾添加两个或以上空格来换行)  
段落第二行

新的段落
***
## <mark>Markdown字体</mark>
*斜体文本*  _斜体文本_  
**粗体文本**  __粗体文本__  
***粗斜体文本***  ___粗斜体文本___  
~~删除线文本~~  
<u>下划线文本</u>  
主体文本[^脚注]

[^脚注]:我也不知道填什么脚注
***
## <mark>Markdown分割线</mark>
一行内使用不少于3个"*"或"-"或"_"来建立一个分割线，行内不能有其他东西
***
---
___
## <mark>Markdown列表</mark>
Markdown支持有序列表和无序列表，无序列表使用"* "或"+ "或"- "作为标记，有序列表使用"1. "、"2. "等作为标记  
* 子项
+ 子项
- 子项
1. 子项
2. 子项 

列表嵌套:只需在子列表的子项前加一个tab（四个空格）即可
* 子项
    * 嵌套子项
    1. 嵌套子项
***
## <mark>Markdown区块</mark>
在段落开头使用"> "即可使用区块,用回车结束段落，即结束区块
> 区块段落1
区块段落1

使用"> > "、"> > > "进行区块嵌套
> 区块
> > 嵌套区块
> >
> > > 嵌套区块

区块中使用列表
> 区块
> 1. 第一项
> 2. 第二项

列表中使用区块
* 第一项
	
	> 内嵌区块
***
## <mark>Markdown代码</mark>
代码区块使用"```"包裹一段代码,并指定一种语言(也可以不指定)
```python
print('python code')
```
***
## <mark>Markdown链接</mark>
这是一个链接 [菜鸟教程](https://www.runoob.com)
这是一个链接 <https://www.runoob.com>

***
## <mark>Markdown图片</mark>
使用方式是："\![图片名]\(图片地址)"
![图片名](http://static.runoob.com/images/runoob-logo.png)
Markdown无法指定图片的高度与宽度，可以使用\<img>标签
<img src="http://static.runoob.com/images/runoob-logo.png" width="60%" height="30px" />

***
## <mark>Markdown表格</mark>
Markdown制作表格使用 | 来分割不同单元格，使用 - 来分割表头和其他行,
*  -:右对齐
*  :-左对齐
*  :-:居中对齐
| 左对齐 | 居中对齐 |右对齐|
| :--- | :---: |---:|
|单元格|单元格|单元格|
***
## <mark>Markdown高级技巧</mark>
* 支持html元素：不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写
* 转义：Markdown 使用了很多特殊符号来表示特定的意义，如果需要显示特定的符号则需要使用转义字符
* 公式编辑：当你需要在编辑器中插入数学公式时，可以使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式来实现