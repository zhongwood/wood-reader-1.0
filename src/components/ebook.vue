<template>
	<div class="ebook">
		<transition name="nav-hide">
			<Nav v-show="ifNavAndFooterShow" class="z-index"/>
		</transition>
		<div class="ebook-wrapper">
			<div id="read"></div>
			<div class="mask">
				<div class="left" @click="prePage"></div>
				<div class="center" @click="navAndFooterShow"></div>
				<div class="right" @click="nextPage"></div>
			</div>
		</div>
		<transition name="footer-hide">
			<Footer  class="z-index" v-show="ifNavAndFooterShow" 
			@setFontSize="setFontSize"
			@setTheme="setTheme"
			@onProgressChange="onProgressChange"
			@jumpTo="jumpTo"
			@navAndFooterShow="navAndFooterShow"
			:defaultFontSize="defaultFontSize" 
			:fontSize="fontSize"
			:ifNavAndFooterShow="ifNavAndFooterShow"
			:defaultTheme="defaultTheme"
			:bookAvailable="bookAvailable"
			:navigation="navigation"
			:themeList="themeList"/>
		</transition>
	</div>
</template>

<script>
import Nav from './navbar.vue'
import Footer from './footer.vue'
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/ebook/2018_Book_AgileProcessesInSoftwareEngine.epub'

export default {
	components:{
		Nav,Footer
	},
	data() {
		return {
			ifNavAndFooterShow:false,
			fontSize:[
				{fontSize:12},
				{fontSize:14},
				{fontSize:16},
				{fontSize:18},
				{fontSize:20},
				{fontSize:22},
				{fontSize:24}
			],							//字号列表
			defaultFontSize:18,			//默认字号
			themeList:[
				{
					name:'default',
					style:{
						body:{
							color:'#333',background:'#fff'
						}
					}
				},
				{
					name:'night',
					style:{
						body:{
							color:'#FFF',background:'#333'
						}
					}
				},
				{
					name:'eye',
					style:{
						body:{
							color:'#333',background:'#A2F29B'
						}
					}
				},
				{
					name:'gold',
					style:{
						body:{
							color:'#333',background:'rgb(241,236,226)'
						}
					}
				},
			],							//主题列表
			defaultTheme:0,				//默认主题
			bookAvailable:false,		//图书是否可用
			navigation:{}
		}
	},
	methods:{
		//电子书的解析渲染
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
			//获取themes对象
			this.themes = this.rendition.themes
			this.themes.fontSize(this.defaultFontSize+"px")
			//注册主题 this.rendition.themes.register(name,style)
			//设置主题 this.rendition.themes.select(name)
			this.registerThemes()
			this.setTheme(this.defaultTheme)
			//获取Locations对象
			//通过钩子函数实现
			this.book.ready.then(() => {
				this.navigation = this.book.navigation	/*获取目录*/
				return this.book.locations.generate()
			}).then(result => {
				this.locations = this.book.locations
				this.bookAvailable = true
			})
		},
		prePage() {		//向前翻页
			this.rendition.prev();
			this.ifNavAndFooterShow = false;
		},
		nextPage() {	//向后翻页
			this.rendition.next();
			this.ifNavAndFooterShow = false;
		},
		navAndFooterShow() {
			this.ifNavAndFooterShow = !this.ifNavAndFooterShow
		},
		setFontSize(s) {
			if(this.themes) {
				this.themes.fontSize(s+"px")
				this.defaultFontSize = s
			}
		},
		registerThemes() {		//注册主题
			this.themeList.forEach((val,index) => {
				this.themes.register(val.name,val.style)
			})
		},
		setTheme(index) {
			this.themes.select(this.themeList[index].name)
			this.defaultTheme = index
		},
		onProgressChange(pro) { 	
			const percentage = pro / 100
			const location  = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0 //获取定位
			this.rendition.display(location)
		},
		jumpTo(href){
			this.rendition.display(href)
			this.ifNavAndFooterShow = false
		}
	},
	mounted() {
		this.showEpub();
	}
}

</script>

<style lang='stylus'>
@import "../assets/styles/global.styl"
.ebook
	.ebook-wrapper
		.mask
			position: absolute
			top: 0
			left: 0
			height:100%
			width:100%
			display:flex
			z-index:100
			.left
				flex:0 0 px2rem(100)
			.center
				flex:1
			.right
				flex:0 0 px2rem(100)
	.icon
		line-height:px2rem(48)
		padding:0 px2rem(10) 
		flex:1
		center()
	.z-index
		z-index:200

	
</style>
