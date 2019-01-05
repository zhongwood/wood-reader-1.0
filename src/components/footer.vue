<template>
	<div class="footer">
		<div class="icon-wrapper" ref="iconWrapper">
			<span class="icon-menu icon active" @click="navigationShow()"></span>
			<span class="icon-git-commit icon active" @click="settingShow(1)"></span>
			<span class="icon-sun icon active" @click="settingShow(2)"></span>
			<span class="icon avtive"  @click="settingShow(3)">A</span>
		</div>
		<!-- setting-font-size -->
		<transition name="footer-hide">
			<div class="setting-wrapper" v-show="ifSettingShow">
				<div class="setting-font-size" v-show="setingNumber === 3">
					<div class="view" :style="{fontSize: fontSize[0].fontSize + 'px'}">A</div>
					<div class="select-wrapper">
						<div class="line-wrapper" v-for="(val,index) in fontSize" @click="setFontSize(val.fontSize)">
							<div class="line"></div>
							<div class="point">
								<div class="outset" v-show="defaultFontSize === val.fontSize">
									<div class="inner"></div>
								</div>
							</div>
							<div class="line"></div>
						</div>
					</div>
					<div class="view" :style="{fontSize: fontSize[fontSize.length-1].fontSize + 'px'}">A</div>
				</div><!-- setting-font-size -->
				<div class="setting-themes" v-show="setingNumber === 2">
					<div class="themes-warpper avtive" v-for="(item,index) in themeList" @click="setTheme(index)">
						<div class="color" :style="{backgroundColor:item.style.body.background}" :class="{'no-border':item.style.body.background !== '#fff'}"></div>
						<div class="name" :class="{'selected':index === defaultTheme}">{{item.name}}</div>
					</div>
				</div><!-- setting-themes -->
				<div class="setting-progress" v-show="setingNumber === 1">
					<div class="progress-warpper">
						<input type="range"
								max="100"
								min="0"
								step="1"
								@change="onProgressChange($event.target.value)"
								@input="onProgressInput($event.target.value)"
								:value="progress"
								:disabled="!bookAvailable"
								ref="progress" />
					</div>
					<div class="text-wrapper">
						<span>{{bookAvailable ? progress + '%' : 'loading...'}}</span>
					</div>
				</div>
			</div>
		</transition>
		<transition name="navigation-slider">
			<div class="navigation" v-show="ifNavigationShow">
				<div class="navigation-wrapper" v-if="bookAvailable">
					<ul>
						<li class="navigation-item active" v-for="(item,index) in navigation.toc" @click="jumpTo(item.href)">{{item.label}}</li>
					</ul>
				</div>
				<div class="loading" v-else>loading...</div>
			</div>
		</transition>
		<transition name="fade">	
			<div class="mask" @click="navigationHide()" v-show="ifNavigationShow"></div>
		</transition>
		<div class="shadow"></div>
	</div>
</template>

<script>

export default {
	props:{
		ifNavAndFooterShow:{
			type:Boolean,
			default:false
		},
		fontSize:{
			type:Array
		},
		defaultFontSize:{
			type:Number
		},
		themeList:{
			type:Array
		},
		defaultTheme:{
			type:Number
		},
		bookAvailable:{
			type:Boolean
		},
		navigation:{
			type:Object
		}
	},
	data() {
		return{
			ifSettingShow:false,
			setingNumber:3,
			progress:0,
			ifNavigationShow:false
		}
	},
	methods:{
		settingShow(n) {
			if(this.setingNumber != n && this.ifSettingShow){
				this.setingNumber = n
			}else{
				this.setingNumber = n
				this.ifSettingShow = !this.ifSettingShow;
			}
		},
		setFontSize(s) {			//设置字体
			this.$emit("setFontSize",s)
		},
		setTheme(index) {			//设置主题
			this.$emit("setTheme",index)
		},
		onProgressChange(value) {	//进度条改变时改变电纸书进度
			this.$emit("onProgressChange",value)
		},
		onProgressInput(value) {	//进度条滑动时
			this.progress = value
			this.$refs.progress.style.backgroundSize=`${this.progress}% 100%`
		},
		navigationShow() {
			this.ifNavigationShow = true
			this.ifSettingShow = false
		},
		navigationHide(){ 		//目录隐藏
			this.ifNavigationShow = false 
			// this.$emit('navAndFooterShow')
		},
		jumpTo(href) {
			this.navigationHide()
			this.$emit('jumpTo',href)
		}
		
	},
	watch:{
		ifNavAndFooterShow:function(n,o) {
			if(!n){
				this.ifSettingShow = false;
			}
		}
	},
	created() {
		this.$nextTick(() => {
			this.$refs.progress.style.backgroundSize=`${this.progress}% 100%`
			
		})
	}
}

</script>

<style lang='stylus'>
@import "../assets/styles/global.styl"
.footer
	transition:all .1s linear
	height:px2rem(48)
	position:absolute
	bottom: 0
	left:0
	width:100%
	.mask
		height:100%
		width:100%
		position:fixed
		z-index:102
		top:0
		left:0
		background:rgba(0,0,0,.4)
		transition:all .1s linear
	.shadow
		height:px2rem(48)
		position:absolute
		bottom: 0
		width:100%
		left:0
		z-index:0
		box-shadow:0px -1px 4px 1px rgba(0, 0, 0, 0.15)
	.icon-wrapper
		position:absolute
		height:px2rem(48)
		bottom: 0
		left:0
		background-color:#fff
		width:100%
		display:flex
		/*line-height:px2rem(48)*/
		font-size:px2rem(20)
		z-index:100
	.setting-wrapper
		position:absolute
		bottom:px2rem(48)
		background-color:#fff
		left:0
		z-index:1
		height:px2rem(60)
		width:100%
		box-shadow:0px -1px 4px 1px rgba(0, 0, 0, 0.15)
		transition:all .1s linear
		font-size:px2rem(14)
		.setting-font-size
			width:100%
			height:100%
			display:flex
			.view
				flex: 0 0 px2rem(40)
				height:100%
				center()
			.select-wrapper
				flex:1
				display:flex
				.line-wrapper
					flex:1
					display:flex
					center()
					height:100%
					&:first-child
						.line
							&:first-child
								opacity:0
					&:last-child
						.line
							&:last-child
								opacity:0
					.line
						flex:1
						height:0
						border-bottom:px2rem(1) solid #ddd
					.point
						width:0
						height:px2rem(5)
						border-left:px2rem(1) solid #ddd
						position:relative
						.outset
							position:absolute
							width:px2rem(16)
							height:px2rem(16)
							border:px2rem(1) solid #ddd
							background-color:#fff
							top:px2rem(-7)
							left:px2rem(-8)
							border-radius:50%
							box-shadow:0px 2px 3px 0px rgba(0, 0, 0, 0.15)
							.inner
								width:30%
								height:30%
								background-color:#333
								border-radius:50%
								margin:0 auto
								margin-top:35%
								box-shadow:0px -1px 4px 1px rgba(0, 0, 0, 0.15)
		.setting-themes
			width:100%
			height:100%
			display:flex
			.themes-warpper
				flex:1
				text-align:center
				display:flex
				flex-direction:column  /* 竖直排布 */
				padding:px2rem(5)
				box-sizing:border-box
				.color
					flex:1
					height:px2rem(14)
					border:px2rem(1) solid #ddd
					box-sizing:border-box
					&.no-border
						border:none
				.name
					center()
					color:#333
					flex:1
					&.selected
						font-weight:700
		.setting-progress
			display:flex
			flex-direction:column
			width:100%
			height:100%
			/*padding:px2rem(5)*/
			.progress-warpper
				flex:1
				center()
				input[type="range"]
					appearance: none
					width:90%
					height:px2rem(2)
					background:-webkit-linear-gradient(#999,#999) no-repeat, #ddd
					background-size:0 100%
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
			.text-wrapper
				flex:1
				center()
	.navigation
		position:fixed
		top:0
		left:0
		height:100%
		width:80%
		font-size:px2rem(18)
		background:#fff
		z-index:103
		transition:all 0.1s linear
		&.navigation-slider-enter,&.navigation-slider-leave-to
			transform:translateX(-100%)
		&.navigation-slider-enter-to,&.navigation-slider-leave
			transform:translateX(0)
		.navigation-wrapper
			padding:px2rem(10) 0
			.navigation-item
				padding:px2rem(10)
				border-bottom:px2rem(1) solid #ddd
				&:last-child
					border:none
		.loading
			center()
			margin-top:px2rem(30)
						

</style>
