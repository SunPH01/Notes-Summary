vue项目：
```
<img :src="item.userIcon ? item.userIcon : ''" :onerror="defaultAvatar">

  computed: {
    defaultAvatar () {
      return 'this.src="'+ require('../../../assets/images/avator@2x.png') +'"'
    }
  }
```
**注意：**`:src="item.userIcon ? item.userIcon : ''" `这里如果写为`:src="item.userIcon" `，那么在item.userIcon=null或者其它一些情况下不会触发onerror。


angularJs:
```
<img ng-src="item.userIcon ? item.userIcon : ''"
onerror="javascript: this.src = '../../images/darkBlue/userSet/avatar.png';"
>
```