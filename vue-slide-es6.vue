<script>
	export default {
		props:['slide','pages'],
		ready(){
			this.$on('slideTo',(num)=>{
				this.turnTo(num)
			})

			this.$on('slideNext',()=>{
				this.nextNav()
			})

			this.$on('slidePre',()=>{
				this.preNav()
			})
		},
		methods:{
			swipeUp(move){
				this.$dispatch('swipeUp',move);
			},
			swipeDown(move){
				this.$dispatch('swipeDown',move);
			},
			reset(){
				let slideTmp=this.pages;
				for(let page of slideTmp){
					page.current=0;
					this.styleCompute(page)
				}
			},
			checkState(){
				this.slide.init.canNext=true;
				this.slide.init.canPre=true;

				let len=this.pages.length;
				if(this.pages[len-2].origin + this.pages[len-2].current == -100){
					this.slide.init.canNext=false;
				}
				if(this.pages[1].origin +this.pages[1].current ==100){
					this.slide.init.canPre=false;
				}
			},
			swipeMove(e){
				if(this.slide.init.tracking){
					if(e.type==='touchmove'){
						e.preventDefault();
						this.slide.init.end.x=e.targetTouches[0].clientX;
						this.slide.init.end.y=e.targetTouches[0].clientY;
					}else{
						e.preventDefault();
						this.slide.init.end.x =e.clientX;
						this.slide.init.end.y =e.clientY;
					}
				}
			},
			swipeStart(e){
				if(e.type ==='touchstart'){
					if(e.touches.length>1){
						this.slide.init.tracking=false;
					}else{
						this.slide.init.tracking=true;
						/*hack*/
						this.slide.init.start.t=new Date().getTime();
						this.silde.init.start.x=e.targetTouches[0].clientX;
						this.slide.init.start.y=e.targetTouches[0].clientY;
					}
				}else{
					this.slide.init.tracking=true;
					/*hack*/
					this.slide.init.start.t=new Date().getTime();
					this.slide.init.start.x=e.clientX;
					this.slide.init.start.y=e.clientY;
				}
			},
			swipeEnd(e){
				this.slide.init.tracking=false;
				let now =new Date().getTime();
				let delayTime =now - this.slide.init.start.t;
				let delayX=this.slide.init.end.x - this.slide.init.start.x;
				let dalayY=this.slide.init.end.y - this.slide.init.start.y;
				if(delayTime >this.slide.init.delayTime){
					return;
				}else{
					/*swipe right*/
					if((dalayX >this.slide.init.delayDistance) && (Math.abs(dalayY) < this.slide.init.delayDistance)){
						this.preNav();
					}
					/*swipe left*/
					else if((-dalayX >this.slide.init.delayDistance) && (Math.abs(delayY)<this.slide.init.delayDistance)){
						this.nextNav();
					}
					/*swipe down*/
					else if((delayY >this.slide.init.delayDistance) && (Math.abs(delayX)<this.slide.init.delayDistance)){
						this.swipeDown(Math.abs(delayY))
					}
					/*swipe up*/
					else if((-delayY>this.slide.init.delayDistance)&& (Math.abs(delayX)<this.slide.init.delayDistance)){
						this.swipeUp(Math.abs(delayY))
					}
				}
			},
			preNav(){
				if(!this.slide.init.canPre) return;

				for(let page of this.pages){
					this.currentCompute(page,false);
				}

				this.slide.init.currentPage--;
				this.checkState();

			},
			nextNav(){
				if(!this.slide.init.canNext) return;
				for(let page of this.pages){
					this.currentCompute(page,true);
				}
				this.slide.init.currentPage++;
				this.checkState();
			},
			turnTo(num){
				let index=Math.ceil(num) -1;
				let len=this.pages.length;
				let step=0;
				if(index >len -1){
					return false;
				}
				this.slide.init.currentPage=Math.ceil(num);

				for(let i=0;i<len;i++){
					if(this.pages[i].current + this.pages[i].origin ==0){
						step=index-i;
						break;
					}
				}
				for(let page of this.pages){
					page.current=page.current -step * 100;
					this.styleCompute(page);
				}
				this.checkState();
			},
			currentCompute(obj,next){
				if(next){
					obj.current =obj.current -100;
				}else{
					obj.current =obj.current +100;
				}
				this.styleCompute(obj)
			},
			styleCompute(obj){
				obj.style['transform'] = `translateX(${obj.origin + obj.current}%)`
			}

		}

	}
</script>
<style>
	.slider{
		position: relative;
		height: 400px;
		top:0;
		left:0;
		color: #fff;
		overflow: hidden;
	}
	.slider-item{
		position: absolute;
		width: 100%;
		height: 100%;
		text-align: center;
		transition: 0.4s ease-in-out transform,opacity;
		-webkit-transition: 0.4s ease-in-out transform,opacity;
		-webkit-transition-duration: 0.4s;
		-webkit-transition-timing-function:ease-in-out ;
	}
</style>
<template>
	<div class="slider" 
	@touchmove="swipeMove"
	@touchstart="swipeStart"
	@touchend="swipeEnd"
	@mousedown="swipeStart"
	@mouseup="swipeEnd"
	@mousemove="swipeMove"
	></div>
</template>