要绘制的思路很简单，当我们按下鼠标的时候，开始画线,将坐标移动到当前点击点，在移动过程中就会产生非常多的点，将这些点连成线不就可以了么，我们首先需要用到**moveTo**将坐标移到我们鼠标点下的点，然后在移动过程中使用**lineTo**将这些点连成线，最后使用**stroke**绘制出来就行，是不是很简单，我们看看具体代码。



```javascript
const cvs = document.getElementById('cvs')
const ctx = cvs.getContext('2d')
let isDownin = false

cvs.addEventListener('mousedown', (e) => {
    isDownin = true
    ctx.moveTo(e.pageX, e.pageY)
})
cvs.addEventListener('mousemove', (e) => {
    if(!isDownin) return
    ctx.lineTo(e.pageX, e.pageY)
    ctx.stroke()
})
cvs.addEventListener('mouseup', (e) => {
    isDownin = false
})

```





完整功能

html:

```html
<canvas id="cvs"></canvas>
<div style="margin-top: 50px">
    <button id="clear">清空</button>
	<button id="back">回退</button>
	<button id="save">保存</button>
</div>
```

js:

```javascript
let width = 800
let height = 400
let isDownin = false
let lastX = 0
let lastY = 0
let cacheData = []

const cvs = document.getElementById('cvs')
const ctx = cvs.getContext('2d')
cvs.width = width
cvs.height = height

cvs.addEventListener('mousedown', (e) => {
	isDownin = true
	lastX = e.pageX;
  lastY = e.pageY;
	let cacheItem = ctx.getImageData(0, 0, width, height);
	cacheData.push(cacheItem)
	ctx.moveTo(lastX, lastY)
})
cvs.addEventListener('mousemove', (e) => {
	if(!isDownin) return
	drawLine(e.pageX, e.pageY)
	ctx.stroke()
})
cvs.addEventListener('mouseup', (e) => {
	isDownin = false
})

function drawLine(x,y){
	ctx.beginPath();
    ctx.lineWidth = 8;
    ctx.strokeStyle = '#fff'; 
		ctx.lineCap = 'round'
    ctx.lineJoin = "round";
    ctx.moveTo(lastX, lastY);
    ctx.lineTo(x, y);
    ctx.stroke();
    lastX = x;
    lastY = y;
}

/* 清空 */
const clear = document.getElementById('clear')
clear.onclick = () => {
	ctx.clearRect(0, 0, width, height)
}

/* 回退 */
const back = document.getElementById('back')
back.onclick = () => {
	if (cacheData.length === 0) return;
  ctx.putImageData(cacheData.pop(), 0, 0);
}

const save = document.getElementById('save')
save.onclick = () => {
	cvs.toBlob((blob) => {
    const a = document.createElement('a');
    document.body.append(a);
    a.download = `签名.png`;
    a.href = URL.createObjectURL(blob);
    a.click();
    a.remove();
  });
}
```

css:

```css
#cvs{
	background-color: #000;
	background: #000;

}
body{
	height: 100vh;
	background-color: rgb(26, 25, 25);

}
```

