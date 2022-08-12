最近做一个功能，一个点击按钮，需要支持拖拽。完成后写在这里做一下笔记。

```react
export default class Record extends Component {
  constructor(props) {
    super(props);
    // 限制最大宽高，不让滑块出去
    this.dragLimit = {
      maxW: null,
      maxH: null,
    };
    this.timer = null;
  }
  // 认购记录拖拽
  recordDrag = (e) => {
    if (this.timer) {
      clearTimeout(this.timer);
      this.timer = null;
    }
    this.timer = setTimeout(() => {
      // 执行点击事件
      window.location.hash = `#${Routers.Record}`;
    }, 500);
    const ev = e || window.event;
    const touch = ev.targetTouches[0];
    const bragBox = document.querySelector('#apply-record');
    const containerBox = document.querySelector('#cal-wrap');

    if (!bragBox || !containerBox) {
      return;
    }

    if (this.dragLimit.maxW === null || this.dragLimit.maxH === null) {
      this.dragLimit = {
        maxW: containerBox.clientWidth - bragBox.offsetWidth,
        maxH: containerBox.clientHeight - bragBox.offsetHeight,
      };
    }

    // 手指触摸开始，记录盒子的初始位置
    const oL = touch.clientX - bragBox.offsetLeft;
    const oT = touch.clientY - bragBox.offsetTop;

    // 触摸中的，位置记录
    bragBox.addEventListener('touchmove', (e1) => {
      if (this.timer) {
        clearTimeout(this.timer);
        this.timer = null;
      }
      const ev1 = e1 || window.event;
      const touch1 = ev1.targetTouches[0];
      let oLeft = touch1.clientX - oL;
      let oTop = touch1.clientY - oT;
      if (oLeft < 0) {
        oLeft = 0;
      } else if (oLeft >= this.dragLimit.maxW) {
        oLeft = this.dragLimit.maxW;
      }
      if (oTop < 0) {
        oTop = 0;
      } else if (oTop >= this.dragLimit.maxH) {
        oTop = this.dragLimit.maxH;
      }
      bragBox.style.left = `${oLeft}px`;
      bragBox.style.top = `${oTop}px`;
    });

    // 触摸结束时的处理
    bragBox.addEventListener('touchend', () => {
      document.removeEventListener('touchmove', (evt) => {
        // 阻止默认事件
        evt.preventDefault();
      });
    });
  }

  render() {
    return (
      <div styleName="wrap" id="cal-wrap">
        ...
        <div id="apply-record" styleName="apply-record" onTouchStart={this.recordDrag} >
          {/* <span>认购记录</span> */}
        </div>
        ...
      </div>
    );
  }
}
```

