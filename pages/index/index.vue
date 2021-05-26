<template>
	<view class="content">
		<view class="child child_happy">
			<animation_sun v-for="item in happySunItems" class="animation-sun"
				v-bind:style="{left:item.left + '%',top:item.top + '%'}"></animation_sun>
			<image class="logo logo_happy" src="/static/1.png" @click="add_sun"></image>
		</view>
		<view class="child child_sad">
			<animation_sad v-for="item in sadItems" class="animation-sun"
				v-bind:style="{left:item.left + '%',top:item.top+50 + '%'}"></animation_sad>
			<image class="logo logo_sad" src="/static/sad1.png" @click="add_sad"></image>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				windowsWidth: 0,
				windowsHeight: 0,
				happySunItems: [],
				sadItems: [],
				happyCount:0,
				sadCount:0,

			}
		},
		onLoad() {
			setInterval(()=>{
				if(this.happyCount > 0 || this.sadCount > 0){
						const db = uniCloud.database();
						
						db.collection('happyrecord').add({
							'HappyCount':this.happyCount,
							'SadCount':this.sadCount,
							'DateTime':new Date().toISOString(),
							'ID':getApp().globalData.ID
						});
				}
				this.happyCount = 0;
				this.sadCount = 0;
			},5*1000)
		},
		methods: {
			add_sun: function() {
				this.happySunItems.push({
					"index": this.happySunItems.length,
					"left": Math.random() * 50,
					"top": Math.random() * 50
				});
				this.happyCount++;
				setTimeout(() => {
					this.happySunItems.pop();
				}, 5000)
			},
			add_sad: function() {
				this.sadItems.push({
					"index": this.sadItems.length,
					"left": Math.random() * 50,
					"top": Math.random() * 50
				});
				this.sadCount++;
				setTimeout(() => {
					this.sadItems.pop();
				}, 5000)
			}
			
		}
	}
</script>

<style>
	page {
		height: 100%;
	}

	.content {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.child{
		width: 100%;
		height: 50%;
		flex-grow:1;
		background-color: #F0ADAA;
	}
	
	.child_happy{
		background-color: #F0ADAA;
	}
	
	.child_sad{
		background-color: #555555;
	}

	.logo {
		position: absolute;
		height: 400rpx;
		width: 400rpx;		
		box-shadow: 0rpx 20rpx 10rpx -20rpx #000;
		border-radius: 200rpx;
	}

	.logo_happy{
		top: 13%;
		left: 25%;
	}

	.logo_sad{
		top: 58%;
		left: 25%;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.animation-sun {
		position: absolute;
		top: 33%;
		left: 25%;
	}
</style>
