## CSS(Cascading Style Sheets)层叠样式表

入门案例

```css
<font style="font-size: 120px;">今天天气好晴朗</font>
```

##### 放置规范

style标签内容体中写css样式代码，该标签放置在head标签之中

##### 格式规范

选择器名称{属性名：属性值；属性名：属性值；....}

选择器：即指定CSS样式作用在哪些HTML标签上

##### 代码规范

属性值为多个时，一个空格分开

注释 /* 注释内容 */

### CSS选择器

#### 元素选择器

以HTML标签名作为选择器名称

标签名{

。。。

}

#### 类选择器

以HTML的类名（class属性值）作为选择器名称

.类名{

。。。

}

#### id选择器

#id{

。。。

}

#### 基本选择器组合方式

层级关系

选择器1 选择器2  ... { //选择器2包裹在选择器1内

/*CSS样式代码*/

}

### 边框属性

##### border设置边框样式

格式 宽度 样式 颜色

solid 实线    none 无边     double 双线

##### width 边框宽度

##### height 高度

##### background-color背景颜色

英文单词

颜色代码 #红绿蓝     每种颜色采用两个16进制位数表示

```css
background-color: #FF11AA;
```

### 布局

##### float浮动

none不浮动 left元素向左浮动  right元素向右浮动

会脱离原有的样式版式，页面样式需要重新调整

### 转换

##### display

HTML中的行内元素和块级元素

块级元素块结束会自动换行 h1,p,div,ul

行内元素不会span，a

block:元素显为块元素

inline:行内元素

none:此元素将被隐藏，不显示也不占用页面空间



### 字体

font-size大小

color颜色



### 盒子模型

##### 边框

border-top

border-right

border-left

border-bottom

通用性设置 border

##### 内边距

padding-top上内边距

通用性设置 padding

##### 外边距

margin-top上外边距

通用性设置margin

### CSS与HTML结合方式

##### 行内样式

针对修改某一个标签样式

标签内style属性写css代码

##### style标签样式

在页面中被多个标签复用

##### 外部样式

css文件中写css代码（工具类）

引入link标签在head中

格式：

```html
<link rel="stylesheet" type="text/css" href="css文件路径"/>
```



