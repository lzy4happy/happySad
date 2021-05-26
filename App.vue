<script>
	export default {
		
		globalData: {  
            screenWidth: '',
			screenHeight:'',
			ID:'',
        },
		onLaunch: function() {
			const res = uni.getSystemInfoSync();
			console.log(res.windowWidth);
			console.log(res.windowHeight);
			this.$scope.globalData.screenWidth = res.windowWidth;
			this.$scope.globalData.screenHeight = res.windowHeight;
			this.login();
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		methods: {
			login:function(){
				uni.login({
				  provider: 'weixin',
				  success: function (loginRes) {
					console.log(loginRes.authResult);
					// 获取用户信息
					uni.getUserInfo({
					  provider: 'weixin',
					  success: function (infoRes) {
						getApp().globalData.ID = infoRes.signature
					  }	
					});
				  }
				});
			},
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
