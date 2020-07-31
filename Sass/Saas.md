### Sass安装

1.安装Ruby

2.`gem install sass`

### Sass编译

1.手动编译

+ ` sass test.scss test.css `

+ ` sass sass/style.scss:css/style.css `

2.自动编译(将sass文件夹中的文件编译为css文件夹中的css文件)

博客讲解: https://blog.csdn.net/weixin_39089928/article/details/88868698 

`sass --watch ./sass:./css`

#### 编译后的格式

 nested：嵌套缩进的css代码，它是默认值。 

 expanded：没有缩进的、扩展的css代码。 

 compact：简洁格式的css代码。 

  compressed：压缩后的css代码。 

 生产环境当中，一般使用最后一个选项。 

```shell
　sass --style compressed test.sass test.css
```

### 基本用法

#### 变量

 SASS允许使用变量，所有变量以$开头。 

```scss
　$blue : #1875e7;　

　　div {
　　　color : $blue;
　　}
```

 如果变量需要镶嵌在字符串之中，就必须需要写在#{}之中。 

```scss
　$side : left;

　　.rounded {
　　　　border-#{$side}-radius: 5px;
　　}
```

#### 计算

 SASS允许在代码中使用算式： 

```scss
body {
　　　　margin: (14px/2);
　　　　top: 50px + 100px;
　　　　right: $var * 10%;
　　}
```

#### 嵌套

```scss

$blue : #1875e7;　
div {
    ul{
        color:$blue;
        li{
            border:1px solid red;
        }
        a{
          color:$blue;
          :hover{
            color:$blue;
          }
        }
    }
}
//css
@charset "UTF-8";
　
div ul {
  color: #1875e7; }
  　
div ul li {
    border: 1px solid red; }
  　
div ul a {
    color: #1875e7; }
    　
div ul a :hover {
      color: #1875e7; }

```

 在嵌套的代码块内，可以使用&引用父元素。比如a:hover伪类，可以写成： 

```scss
$blue : #1875e7;　
div {
    ul{
        color:$blue;
        li{
            border:1px solid red;
        }
        a{
          color:$blue;
          &:hover{
            color:$blue;
          }
        }
    }
}
//css
@charset "UTF-8";
　
div ul {
  color: #1875e7; }
  　
div ul li {
    border: 1px solid red; }
  　
div ul a {
    color: #1875e7; }
    　
div ul a:hover {
      color: #1875e7; }

```

属性嵌套

```scss
body{
    font{
        family:'';
        size:12px;
        weight:bold
    }
}
```

#### mixin

类似与JS中的函数   @mixin  名字(参数1，参数2){}

 使用@mixin命令，定义一个代码块。 

```scss
@mixin left {
　　　　float: left;
　　　　margin-left: 10px;
    a{
        color:red;
    }
　　}
```

 使用@include命令，调用这个mixin。 

```scss
　div {
　　　　@include left;
　　}
```

 mixin的强大之处，在于可以指定参数和缺省值。 

```scss
@mixin left($value: 10px) {
　　　　float: left;
　　　　margin-right: $value;
　　}
```

 使用的时候，根据需要加入参数： 

```scss
div {
　　　　@include left(20px);
　　}
```

#### 继承

 SASS允许一个选择器，继承另一个选择器。比如，现有class1： 

```scss
.class1 {
　　　　border: 1px solid #ddd;
　　}
```

 class2要继承class1，就要使用@extend命令： 

```scss
.class2 {
　　　　@extend .class1;
　　　　font-size:120%;
　　}
```

#### 引入Partials

以_开头，比如   _base.scss  ，不会编译

```scss
@import 'base'
.class1 {
　　　　border: 1px solid #ddd;
　　}
```

#### 条件语句

```scss
$size:false;
    div{
        @if @size {
           border:1px solid red;
        }
        border:2px solid red;
       }
//比如
$theme :'dark';
body{
    @if @theme == dark{
        background-color:black;
    }@else if $theme == ligth{
        background-color:white;
    }@else{
        background-color:red;
    }
}
```

#### @for循环

```scss
#columes:4
@for $i from 1 through $columes {
    .col-#{$i}{
        width:100%/$columes *$i;
    }
}
//css

.col-1{
    width:25%;
}
.col-2{
    width:50%;
}
.col-3{
    width:75%;
}
.col-4{
    width:100%;
}
```

#### @each

```scss
$icons:sucess error warning //列表
    @each $icon in $icons{
        .icon-#{$icon}{
            　background-image: url("/image/#{$icon}.jpg");
            }
        }
```

#### @while

```scss
$i:6;
@while $i >0{
    .item-#{$i} {
        width:5px*$i;
    }
    $i:$i-2;
}
```

#### 函数

```scss
$colors:(light:#fff,dark:#000);
@function color($key){
    @return map-get($colors,$key);
}

body{
    background-color:color(light);
}
```

