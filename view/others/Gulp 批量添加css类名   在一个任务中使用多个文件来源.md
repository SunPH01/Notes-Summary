**1.首先安装环境**
1.安装gulp： npm install gulp
2.安装gulp-clean-css npm install gulp-clean-css
3.安装gulp-css-wrap npm install gulp-css-wrap
4.npm i gulp merge-stream //在一个任务中使用多个文件来源

**2.在项目根目录新建gulpfile.js文件**
写入以下代码
```javascript
var path = require('path')
var gulp = require('gulp')
var cleanCSS = require('gulp-clean-css')
var cssWrap = require('gulp-css-wrap')
var merge = require('merge-stream');
 
gulp.task('css-wrap', function () {
    var light = gulp.src(path.resolve('./src/assets/scss/theme/light/index.css'))
    /* 找需要添加命名空间的css文件，支持正则表达式 */
    .pipe(cssWrap({
        selector: '.theme-light' /* 添加的命名空间 */
    }))
    .pipe(cleanCSS())
    .pipe(gulp.dest('./src/assets/scss/theme/css/light')); /* 存放的目录 */
 
    var dark = gulp.src(path.resolve('./src/assets/scss/theme/dark/index.css'))
    /* 找需要添加命名空间的css文件，支持正则表达式 */
    .pipe(cssWrap({
        selector: '.theme-dark' /* 添加的命名空间 */
    }))
    .pipe(cleanCSS())
    .pipe(gulp.dest('./src/assets/scss/theme/css/dark')); /* 存放的目录 */
 
    return merge(light,dark);
})
```
　然后在命令行运行：```gulp css-wrap ``` 就生成了添加过类名的css文件啦