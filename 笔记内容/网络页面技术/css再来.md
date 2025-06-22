

### 以下是五种让 div 中的内容[水平垂直居中](https://so.csdn.net/so/search?q=水平垂直居中&spm=1001.2101.3001.7020)的方法：

1. 使用 Flex 布局

使用 Flex 布局是让 div 中的内容水平垂直居中的最常见方法，可以使用以下样式实现：

```css
.container {



  display: flex;



  justify-content: center;



  align-items: center;



}
```



以上样式会将 `container` 容器中的内容水平、[垂直居中](https://so.csdn.net/so/search?q=垂直居中&spm=1001.2101.3001.7020)。

1. 使用绝对定位

使用[绝对定位](https://so.csdn.net/so/search?q=绝对定位&spm=1001.2101.3001.7020)也可以实现 div 中内容的水平垂直居中，可以使用以下样式实现：

```css
.container {



  position: relative;



}



 



.content {



  position: absolute;



  top: 50%;



  left: 50%;



  transform: translate(-50%, -50%);



}
```



以上样式会将 `content` 容器中的内容水平、垂直居中。

1. 使用 table-cell

使用 table-cell 也可以实现 div 中内容的水平垂直居中，可以使用以下样式实现：

```css
.container {



  display: table-cell;



  text-align: center;



  vertical-align: middle;



}
```



以上样式会将 `container` 容器中的内容水平、垂直居中。

1. 使用 Grid 布局

使用 Grid 布局也可以实现 div 中内容的水平垂直居中，可以使用以下样式实现：

```css
.container {



  display: grid;



  place-items: center;



}
```



以上样式会将 `container` 容器中的内容水平、垂直居中。

1. 使用 line-height

使用 line-height 属性也可以实现 div 中内容的水平垂直居中，可以使用以下样式实现：

```css
.container {



  line-height: 100px; /* 修改此处的值为 div 的高度 */



  text-align: center;



}
```

以上样式会将 `container` 容器中的内容水平、垂直居中。

以上五种方法都能实现 div 中内容的水平垂直居中，具体使用哪种方法可以根据具体的项目需求和适用场景来决定。

### elment ul

  <el-menu  menu-trigger="click

| 子菜单打开的触发方式(只在 mode 为 horizontal 时有效) | string | hover / click | hover |
| ---------------------------------------------------- | ------ | ------------- | ----- |
|                                                      |        |               |       |

<el-col :span="4">          <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>        </el-col>

