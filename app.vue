<template>
	<div class="container">
		<div class="slideline">
			<div class="item" @click="turnTo(1)" :class='{"active":slide.init.currentPage ==1}'>page1</div>
			<div class="item" @click="turnTo(2)" :class='{"active":slide.init.currentPage ==2}'>page2</div>
			<div class="item" @click="turnTo(3)" :class='{"active":slide.init.currentPage ==3}'>page2</div>
		</div>
		<img src="./assets/images/slide-arrow-left.png"
			class="slider-left"
			@click="slidePre"
			:class='{"disable":!this.slide.init.canPre}'
		>
		<img src="./assets/images/slide-arrow-right.png"
			class="slider-right"
			@click="slideNext"
			:class='{"disable":!this.slide.init.canNext}' 
		>
		<slide :page="someList" :slide="slide">
			<div
				v-for="item in someList"
				class="slider-item page{{$index}}"
				:style="someList[$index].style"
				>
				<h1>{{item.title}}</h1>
				<button @click="turnTo(($index+2))">to page{{$index+1}}</button>
			</div>
		</slide>
	</div>
</template>
<script>
	import slide from 'vue-slide'
	export default {
		data(){
			return {
				someList:[
				{
					title:'1',
					origin:0,
					current:0,
					style:{
						'background-image':'url(dist/testimg-1.png)',
						'background-size':'cover',
						'transform':`translateX(0%)`
					}
				}],
				slide:{
					init:{
						pageNum:3,
						currentPage:1,
						canPre:false,
						start:{},
						end:{},
						trackimg:false,
						delayTime:500,
						delayDistance:100
					}
				}
			}
		},
		methods:{
			turnTo(num){
				this.$broadcast('slideTo',num)
			},
			slideNext(){
				this.$broadcast('slideNext')
			},
			slidePre(){
				this.$broadcast('slidePre')
			}
		},
		components:{
			slide
		}
	}
</script>