<!-- 本示例未包含完整css，获取外链css请参考上文，在hello uni-app项目中查看 -->
<template>
    <view>
       
        <picker-view v-if="visible" :indicator-style="indicatorStyle" :value="value" @change="bindChange" class="picker-view">
            <picker-view-column>
                <view class="item" v-for="(item,index) in years" :key="index">{{item}}年</view>
            </picker-view-column>
            <picker-view-column>
                <view class="item" v-for="(item,index) in months" :key="index">{{item}}月</view>
            </picker-view-column>
           <!-- <picker-view-column>
                <view class="item" v-for="(item,index) in days" :key="index">{{item}}日</view>
            </picker-view-column> -->
        </picker-view>、
		<view class="button_group">
			<button type="primary" class="button_item" @click="back">确定</button>
			<button type="default"  class="button_item">取消</button>
		</view>
    </view>
</template>
<script>
    export default {
        data: function () {
            const date = new Date()
            const years = []
            const year = date.getFullYear()
            const months = []
            const month = date.getMonth() + 1
            const days = []
            const day = date.getDate()
            for (let i = 1990; i <= date.getFullYear(); i++) {
                years.push(i)
            }
            for (let i = 1; i <= 12; i++) {
                months.push(i)
            }
            for (let i = 1; i <= 31; i++) {
                days.push(i)
            }
            return {
                title: '选择时间',
                years,
                year,
                months,
                month,
                days,
                day,
                value: [9999, month - 1, day - 1],
                visible: true,
                indicatorStyle: `height: 50px;`
            }
        },
        methods: {
            bindChange: function (e) {
                const val = e.detail.value
                this.year = this.years[val[0]]
                this.month = this.months[val[1]]
                this.day = this.days[val[2]]
            },
			back(){
				let pages = getCurrentPages();  //获取所有页面栈实例列表
				let nowPage = pages[ pages.length - 1];  //当前页页面实例
				let prevPage = pages[ pages.length - 2 ];  //上一页页面实例
				var dateStr = this.year + '-' + this.month + '-' + '01'
				prevPage.$vm.getDb(new Date(dateStr));
				uni.navigateBack({
					delta:1,
					
				})
			}
        }
    }
</script>
<style>
    .picker-view {
        width: 750rpx;
        height: 600rpx;
        margin-top: 20rpx;
    }
    .item {
        height: 50px;
        align-items: center;
        justify-content: center;
        text-align: center;
    }
	.button_group{
		width: 100%;
		flex-grow: 1; 
		position: absolute;
		bottom: 200rpx;
		padding: 20rpx;
	}
	
	.button_item{
		margin: 20rpx;
	}
</style>