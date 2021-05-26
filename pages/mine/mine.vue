<template>
	<view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad() {
			this.login();
		},
		methods: {
			login(){
				uni.login({
				  provider: 'weixin',
				  success: function (loginRes) {
				    console.log(loginRes.authResult);
				    // 获取用户信息
				    uni.getUserInfo({
				      provider: 'weixin',
				      success: function (infoRes) {
				        console.log(infoRes);
				      }
				    });
				  }
				});
			}
			
		}
	}
</script>

<style>

</style>
