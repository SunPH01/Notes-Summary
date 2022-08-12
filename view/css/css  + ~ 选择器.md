# 1. + 相邻兄弟选择器
如果需要选择紧接在另一个元素后的元素，而且二者有相同的父元素，可以使用相邻兄弟选择器（Adjacent sibling selector）。

例如，如果要增加紧接在 h1 元素后出现的段落的上边距，可以这样写：
`h1 + p {margin-top:50px;}`
这个选择器读作：“选择紧接在 h1 元素后出现的段落，h1 和 p 元素拥有共同的父元素”。

相邻兄弟选择器使用了加号（+），即相邻兄弟结合符（Adjacent sibling combinator）。
注释：与子结合符一样，相邻兄弟结合符旁边可以有空白符。
请看下面这个文档树片段：
```
<div>
  <ul>
    <li>List item 1</li>
    <li>List item 2</li>
    <li>List item 3</li>
  </ul>
  <ol>
    <li>List item 1</li>
    <li>List item 2</li>
    <li>List item 3</li>
  </ol>
</div>
```
在上面的片段中，div 元素中包含两个列表：一个无序列表，一个有序列表，每个列表都包含三个列表项。这两个列表是相邻兄弟，列表项本身也是相邻兄弟。不过，第一个列表中的列表项与第二个列表中的列表项不是相邻兄弟，因为这两组列表项不属于同一父元素（最多只能算堂兄弟）。

请记住，用一个结合符只能选择两个相邻兄弟中的第二个元素。请看下面的选择器：

li + li {font-weight:bold;}
上面这个选择器只会把列表中的第二个和第三个列表项变为粗体。第一个列表项不受影响。

# 2. \>子选择器
**\>** 子选择器选择一个元素的直接子元素。
例如，如果您希望选择只作为 h1 元素子元素的 strong 元素，可以这样写：
```h1 > strong {color:red;}```
这个规则会把第一个 h1 下面的子 strong 元素变为红色，但是div里的 strong 不受影响：

```
<h1>This is <strong>very</strong> <div><strong>very</strong></div> important.</h1>
```
# 3. ~ 同层全体组合选择器
选择所有跟在article后的同层article元素，不管它们之间隔了多少其他元素：

```
article ~ article { border-top: 1px dashed #ccc }
```
**[attribute~=value]**   选择器用于选择属性值包含一个指定值的元素。