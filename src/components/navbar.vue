<template>
	<div class="nav" >
		<div class="back"><span class="icon-arrow_lift icon"></span></div>
		<div class="main">
			<div class="icon-wrapper">
				<span class="icon-user icon active"></span>
				<span class="icon-shopping-cart icon active"></span>
				<span class="icon-more-vertical icon active"></span>
			</div>
		</div>
	</div>
</template>

<script>

export default {
	
}

</script>

<style lang='stylus'>
@import "../assets/styles/global.styl"
.nav
	position:absolute
	top: 0
	left:0
	height:px2rem(48)
	background-color:#fff
	z-index:101
	width:100%
	display:flex
	/*line-height:px2rem(48)*/
	font-size:px2rem(20)
	box-shadow:0px 1px 4px 1px rgba(0, 0, 0, 0.15)
	transition:all .1s linear
	.back
		flex:0 0 px2rem(50)
		center()
	.main
		flex:1
		display:flex
		justify-content: flex-end
		.icon-wrapper
			flex:0 0 px2rem(40)
			height:100%
			line-height:px2rem(48)
			center()
</style>
