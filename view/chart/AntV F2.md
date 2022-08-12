#### 一、面积图

```javascript
const chart = new F2.Chart({
    id,
    pixelRatio: window.devicePixelRatio,
    padding: 0
  })
  chart.source(data, {
    time: {
      type: 'timeCat',
      tickCount: 3,
      range: [0, 1]
    },
    tem: {
      tickCount: 5,
      min: 0
    }
  })
  chart.axis(false)
  chart.tooltip(false)
  chart.area()
    .position('time*tem')
    .color('l(90) 0:#86CDF3 1:#C1EDFC') // 面积区域背景色渐变
    .style({ fillOpacity: 1 })  // 背景色透明度
    .shape('smooth')
  chart.line()
    .position('time*tem')
    .color('#5592F0')
    .shape('smooth')
  chart.render()
```
#### 二、折线图

```javascript
const chart = new F2.Chart({
      id: 'myChart',
      pixelRatio: window.devicePixelRatio, // 指定分辨率
      plugins: Tooltip,
      // padding: ['auto', 60, 'auto', 50],
      width: window.innerWidth,
    });

    chart.source(this.state.data, {
      date: {
        type: 'timeCat',
        tickCount: 5,
        mask: 'MM-DD',
        range: [0, 1],
      },
      value: {
        tickCount: 4,
      },
    });

    // 设置x Y轴
    const lineCfg = {
      lineWidth: 1,
      stroke: this.color.axisLine,
      top: true, // 展示在最上层
    };
    chart.axis('date', {
      label: (text, index, total) => { // 轴线文字标签
        const textCfg = {
          fill: this.color.font,
        };
        // textCfg.text = dateFormat(Number(text), 'MM-dd');
        textCfg.textAlign = 'center';
        if (index === 0) {
          textCfg.textAlign = 'left';
        } else if (index > 0 && index === total - 1) {
          textCfg.textAlign = 'end';
        }
        return textCfg;
      },
      grid: () => {
        return {
          stroke: this.color.axisLine,
          lineDash: [0],
        };
      },
      tickLine: null, // 刻度标识线
      line: lineCfg, // x轴线配置
      labelOffset: 10, // 坐标轴文本距离轴线的距离
    });
    chart.axis('value', {
      label: (text) => {
        let val = `${Number(text).toFixed(1)}%`;
        if (text === '0') {
          val = text;
        }
        return {
          text: val,
          fill: this.color.font,
        };
      },
      line: lineCfg,
      grid: (text) => {
        if (text === '0') { // 栅格线着重显示
          return {
            stroke: this.color.axisLine,
            lineDash: [4],
          };
        }
        return null;
      },
    });

    // chart.guide().text({
    //   position: [0, 70],
    //   content: '倍数',
    //   offsetX: -20,
    //   top: true,
    // });
    // chart.guide().text({
    //   position: [14, -10],
    //   content: '(时间)',
    //   top: true,
    //   offsetX: 10
    // });

    // 图例
    chart.legend('name', {
      position: 'bottom',
      align: 'center',
      marker: 'square',
      offsetY: -10, // 图例上偏移距离
      itemWidth: 110,
      nameStyle: {
        fill: this.color.font,
      },
      itemFormatter(val) {
        if (val === 'signalData') {
          return '新股智能盈策略';
        }
        return '恒生指数'; // val 为每个图例项的文本值
      },
      clickable: false,
    });

    // tooltip
    chart.tooltip({
      showCrosshairs: true, // 是否显示辅助线，点图、路径图、线图、面积图默认展示
      crosshairsStyle: {
        stroke: 'rgba(0, 0, 0, 0.5)',
        lineWidth: 1,
        lineDash: [2], // 虚线
      }, // 配置辅助线的样式
      background: {
        radius: 2,
        fill: '#ccc',
        padding: [6, 10],
      }, // tooltip 内容框的背景样式
      snap: true,
      showItemMarker: false, // 是否展示每条记录项前面的 marker
      custom: true, // 是否自定义 tooltip 提示框
      onHide() {
        const tooltipEl = document.getElementsByClassName('f2-tooltip')[0];
        if (tooltipEl) {
          tooltipEl.style.display = 'none';
        }
      }, // tooltip 隐藏时的回调函数
      onChange(ev) {
        const tooltipEl = document.getElementsByClassName('f2-tooltip')[0];
        // const currentData = _.minBy(ev.items, item => item.y);
        if (tooltipEl) {
          tooltipEl.innerHTML = `<p><span>新股智能盈策略：</span><span>${ev.items[0].value}${ev.items[0].value === '0' ? '' : '%'}</span></p><p><span>恒生指数：</span><span>${ev.items[1].value}${ev.items[1].value === '0' ? '' : '%'}</span></p><p><span>日期：</span><span>${dateFormat(Number(ev.items[0].origin.date), 'yyyy-MM-dd')}</span></p>`;
          tooltipEl.style.display = 'block';
          // tooltipEl.style.left = `${(_this.canvasWrap.offsetLeft + currentData.x) - (tooltipEl.offsetWidth / 2)}px`;
          // tooltipEl.style.top = `${(_this.canvasWrap.offsetTop + currentData.y) - tooltipEl.offsetHeight - 15}px`;
        }
      }, // tooltip 内容发生改变时的回调函数
      crosshairsType: 'xy', // 辅助线的种类
    });

    chart.line().position('date*value').color('name', ['#4B95D6', '#fc724c']).shape('smooth')
      .style({ lineWidth: 1 });
    chart.render();
```