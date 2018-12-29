# my-reader

> A Vue.js project
[reader1.0]{http://zhongwood.likesyou.org/reader1.0/}


## 笔记

### 1.限制屏幕缩放

```HTML
<meta name="viewport" content="width=device-width,initial-scale=1.0.maxmum-scale=1.0,minmum-scale=1.0,user-scalable=no">
```

### 2.初始化字体大小

```javascript
document.addEventListener('DOMContentLoaded',() => {
  const html = document.querySelector("html");
  let fontSize = window.innerWidth / 10;
  fontSize = fontSize > 50 ? 50 : fontSize;
  html.style.fontSize = fontSize + "px"
})
```

### 3.实例化ePub

```javascript
	showEpub() {
		//生成book对象
		this.book = new Epub(DOWNLOAD_URL);

		// 生成rendition对象
		this.rendition = this.book.renderTo('read',{
			width:window.innnerWidth,
			height:window.innerHeight
		});

		// 渲染到屏幕
		this.rendition.display()

		this.book.ready.then(() => {
			this.navigation = this.book.navigation	/*获取目录*/
			return this.book.locations.generate()
		}).then(result => {
			this.locations = this.book.locations
			this.bookAvailable = true
		})
	}
```

```javascript
	//向前翻页
	this.rendition.prev()
	//向后翻页
	this.rendition.next()
	//获取themes对象 设置字体
	this.themes = this.rendition.themes
	//注册主题 this.rendition.themes.register(name,style)
	//设置主题 this.rendition.themes.select(name)
```
```javascript
	//页面跳转函数
	onProgressChange(pro) {
		const percentage = pro / 100
		const location  = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0 //获取定位
		this.rendition.display(location)
	}
```

### 4.CCC flex

```css
	display:flex
	flex-direction:column  /* 竖直排布 */
```

### 5.input[type='range']

```javascript
	onProgressInput(value) {
		this.progress = value
		this.$refs.progress.style.backgroundSize=`${this.progress}% 100%` //左边
	}
```

```css
	input[type="range"]
		appearance: none
		width:90%
		height:px2rem(2)
		background:-webkit-linear-gradient(#999,#999) no-repeat, #ddd //重点
		background-size:0 100%	//重点
		&::-webkit-slider-runnable-track 	/*进度线*/
			border-raduis:px2rem(1)
		&::-webkit-slider-thumb   			/*滑块*/
			appearance: none
			width:px2rem(16)
			height:px2rem(16)
			border:px2rem(1) solid #ddd
			background-color:#fff
			border-radius:50%
			box-shadow:0px 2px 3px 0px rgba(0, 0, 0, 0.15)
		&:focus
			outline:none
```
