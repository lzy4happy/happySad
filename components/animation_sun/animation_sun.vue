<template>
	<view>
		<view  class="notes">
			<view class="n n-1">
				<image class="nimg" src="/static/1.png" ></image>
			</view>
			<view class="n n-2">
				<image class="nimg" src="/static/5.png" ></image>
			</view>
			<view class="n n-3">
				<image class="nimg" src="/static/7.png"></image>
			</view>
			<view class="n n-4">
				<image class="nimg" src="/static/9.png"></image>
			</view>
			<view class="n n-5">
				<image class="nimg" src="/static/8.png"></image>
			</view>
			<view class="n n-6">
				<image class="nimg" src="/static/6.png"></image>
			</view>
		</view>
	</view>
</template>

<script>
	
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
	.notes {
		  display: block;
		  margin: auto;
		  width: 30%;
		  min-width: 60px;
		  height: 40px;
		  border: 0px solid #000;
	}
	
	.n{
		position: absolute;
		animation: notes 2s infinite linear;
		font-size: 35px;
		opacity: 0;
		
	}
	
	.n-1 {
	  margin-left: 0;
	  animation-delay: 0s;
	}
	
	.n-2 {
	  margin-left: 30%;
	  animation-delay: 1.5s;
	}
	
	.n-3 {
	  margin-left: 60%;
	  animation-delay: 1s;
	}
	
	.n-4 {
	  margin-left: 10%;
	  animation-delay: 2s;
	}
	
	.n-5 {
	  margin-left: 45%;
	  animation-delay: 2.5s;
	}
	
	.n-6 {
	  margin-left: 75%;
	  animation-delay: 0.5s;
	}
	
	@keyframes notes {
	  0% {
	    transform: scale(1) translate(0, 0);
	    opacity: 0;
	  }
	  50% {
	    opacity: 1;
	    transform: scale(1.5) translate(50%, -50%);
	  }
	  100% {
	    transform: scale(1.5) translate(100%, -100%);
	    opacity: 0;
	  }
	}
	
	.nimg {
		width: 50rpx;
		height: 50rpx;
	}
</style>
