# sticky 定位
sticky 英文字面意思是粘，粘贴，所以可以把它称之为粘性定位。
position: sticky; 基于用户的滚动位置来定位。
粘性定位的元素是依赖于用户的滚动，在 position:relative 与 position:fixed 定位之间切换。
它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。
元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。
这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。
**注意：**Internet Explorer, Edge 15 及更早 IE 版本不支持 sticky 定位。 Safari 需要使用 -webkit- prefix (查看以下实例)。
```
.sticky {
    position: -webkit-sticky; /* Safari */
    position: sticky;
    top: 0;
    background-color: green;
}
```
![image.png](https://upload-images.jianshu.io/upload_images/18030682-ce89cd8ee3372775.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当滚动条向下滑动，该元素top值为0时就会粘贴在滚动盒子顶部不再继续往上滚动：
![image.png](https://upload-images.jianshu.io/upload_images/18030682-f97bf0ffe4a4e48f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)