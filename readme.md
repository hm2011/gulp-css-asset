# gulp-css-asset

## 描述

一个gulp插件，用于处理css文件及其引入的各种资源文件。

## 安装

```bash
npm install gulp-css-asset --save-dev
```


## 用法

```javascript
var fs = require('fs');
var path = require('path');

var gulp = require('gulp');
var cssAsset = require('gulp-css-asset');

gulp.task('style', function(){
    return gulp.src('./src/style/**/*.css', { base: './src/' })
    	.pipe(cssAsset({
            rootPath: './src/', // css中以'/'开头的路径所引用文件的路径基准
            staticPath: 'http://www.test.com/static/', // 最终静态文件存放位置
            base64: function(filePath){
                return fs.statSync(filePath).size < 1024; // 对小于1kb（1024b）的资源文件进行base64转码
            },
            sprite: function(imagePath){
                if( /^_/.test(path.basename(imagePath)) ){
                    return 'style/image/icons.png'; // 返回合并后的图片相对于base（'./src/'）的文件名路径
                } else {
                    return ''; // 返回空字符串表示不合并该图片
                }
            }
        }))
        .pipe(gulp.dest('./dist/'));
});
```



## 配置

可以向该插件传入一个选项对象`options`来配置某些处理逻辑。


### options.cwd

值类型：string

默认值：`process.cwd`

此选项将作为引用资源文件的 cwd， 默认为`process.cwd`，一般不需要指定此选项。



### options.base

值类型：string

默认值：`gulp.src()`的 base 选项值（被处理的css文件的base）

此选项将作为引用资源文件的 base， 默认为处理的 css 文件的 base，即`gulp.src()`的 base 选项值，一般不需要指定此选项。



### options.rootPath

值类型：string

默认值：`options.base`

此选项用来指定css文件中以`/`开头的URL的基准路径，以便能够找到该引用资源文件。如果该值是相对路径则基于 `process.cwd`。

其他类型的URL的处理方式如下：

-   不处理以`"//"`、`"http://"`或`"https://"`开头的路径
-   其他相对路径基于其所在css文件的路径



### options.staticPath

值类型：string | function

默认值：`"/"`

此选项用于指定处理后的css文件中引用资源的url的前缀，必须是一个以`"/"`、`"//"`、`"http://"`或`"https://"`开头的路径字符串。

该选项在URL中的作用相当于`options.base`在路径中的作用，提供一个绝对路径来组织所有静态资源。



### options.base64

值类型：boolean | function

默认值：`false` （表示不进行base64转码）

此选项用于指定是否对资源文件进行base64转码。当为`true`时，表示所有引用的资源文件都进行base64编码，为`false`时，表示所有引用的资源文件均不进行base64编码。当
值为function类型时，该function会接收一个资源文件的完整文件路径字符串作为参数，这样您可以精确针对每一个引用资源进行设置，函数的返回值应当为一个boolean类型，
如果返回值不是boolean类型则会被转化成boolean类型来处理。



### options.sprite

值类型：string | function

默认值：`""` （空字符串表示不合并图片）

此选项用于指定精灵图合并后的图片文件名路径，必须是一个相对于`options.base`的相对文件路径（如：`"./icon/sprite.png"`）。

其值可以是一个相对于`options.base`的相对文件路径的字符串，也可以是一个返回该相对于`options.base`的相对文件路径的字符串的函数，此函数有唯一一个`filePath`，它
表示引用的资源文件的完整文件路径，使用者可以根据该文件路径来决定是否要将其作为精灵图来合并，如果不想合并该图片，返回空字符串即可，否者返回一个其合并后的文件
相对于`options.base`的相对文件路径的字符串。

本工具只支持将 jpg、png、gif(非动画) 类型的图片合并成 png 图片。



### options.tabSize

值类型：number（自然数）

默认值：`0`

此选项由于设置生成代码的缩进字符。必须为一个自然数：当值为`0`时使用制表符`"\t"`，其他值表示使用对应数量的空格进行缩进。




## 更新历史

*  **0.1.0** 
    +   添加合并精灵图功能
    +   添加base64转码功能
    +   添加更改路径功能



## 许可信息

MIT
