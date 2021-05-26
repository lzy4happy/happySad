<template>
	<view class="content">

		<uni-ec-canvas class="uni-ec-canvas" id="uni-ec-canvas" ref="uni-ec-canvas" canvas-id="uni-ec-canvas" :ec="ec"
			@inited="inited"></uni-ec-canvas>

		<button type="primary" class="uni-padding-wrap" @click="goto('/pages/select-time/select-time')">
			<view class="uni-title">日期：{{year}}年{{month}}月</view>
		</button>
	</view>
</template>

<script>
	import uniEcCanvas from "@/components/uni-ec-canvas/uni-ec-canvas";
	import * as echarts from "@/components/uni-ec-canvas/echarts";
	export default {
		data() {

			return {
				cellSize: [200, 200],
				pieRadius: 20,
				scatterData: "",
				pieInitialized: "",
				IsShow: false,
				range:['2021-05'],
				startDate:'',
				endDate:'',
				ec: {

					option: {
						tooltip: {},
						legend: {
							data: ['开心', '伤心'],
							bottom: 20
						},
						calendar: {
							width: getApp().globalData.screenWidth,
							height: getApp().globalData.screenWidth,
							top: 'middle',
							left: 'center',
							orient: 'vertical',
							cellSize: this.cellSize,
							yearLabel: {
								show: false,
								fontSize: 30
							},
							dayLabel: {
								margin: 20,
								firstDay: 1,
								nameMap: ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
							},
							monthLabel: {
								show: false
							},
							range: ['2021-05'],
						},
						series: [{
							id: 'label',
							type: 'scatter',
							coordinateSystem: 'calendar',
							symbolSize: 1,
							label: {
								normal: {
									show: true,
									formatter: '{c}',
									offset: [-10, -20],
									fontSize: 12
								}

							},
							data: this.scatterData
						}]
					},
				},
				year: 2021,
				month: 5,
			}
		},

		onInit: function(options) {

		},

		onLoad: function(options) {
			var date = new Date();
			date = new Date((date.getYear() + 1900) + '-' + (date.getMonth() + 1) + '-01')
			this.reSet(date);	
			console.log(this.range);
			var endDate = new Date(date);
			endDate.setMonth(date.getMonth() + 1);
			this.scatterData = this.getVirtulData(date,endDate);
			
		},

		

		onResize: function(options) {
			if (this.pieInitialized) {
				this.ec.option.series = this.getPieSeriesUpdate(this.scatterData, this.$refs['uni-ec-canvas']
					.$curChart);
			}
		},
		methods: {
			inited(chart) {
				this.pieInitialized = true;
				this.getDb(new Date());
				//this.ec.option.series = this.getPieSeries(this.scatterData, this.$refs['uni-ec-canvas'].$curChart);
				//this.ec.option.series = this.getPieSeriesUpdate(this.scatterData, this.$refs['uni-ec-canvas'].$curChart);								
			},
			reSet:function(date){
					this.year = date.getYear()+1900;
					this.month = date.getMonth() + 1;
					var str = this.year+'-' + this.month;
					this.ec.option.calendar.range = [str];
			},
			goto:function(url) {
				
				uni.navigateTo({
					url: url
				})
			},
			getVirtulData: function(startDate,endDate) {
				var startStr = (startDate.getYear() + 1900) + '-' + (startDate.getMonth() + 1) + '-01'
				var endStr = (endDate.getYear() + 1900) + '-' + (endDate.getMonth() + 1) + '-01'
				var date = +echarts.number.parseDate(startStr);
				var end = +echarts.number.parseDate(endStr);
				var dayTime = 3600 * 24 * 1000;
				var data = [];
				for (var time = date; time < end; time += dayTime) {
					data.push([
						echarts.format.formatTime('yyyy-MM-dd', time),
						echarts.format.formatTime('dd', time),
					]);
				}
				return data;
			},
			getCount: function(data, isHappy, dateTime) {
				var count = 0;
				for (var i = 0; i < data.length; i++) {
					var dbDateTime = new Date(data[i].DateTime);
					if (dbDateTime.getYear() === dateTime.getYear() &&
						dbDateTime.getMonth() === dateTime.getMonth() &&
						dbDateTime.getDate() === dateTime.getDate()) {
						if (isHappy)
							count += data[i].HappyCount;
						else
							count += data[i].SadCount;
					}
				}

				return count;
			},
			getDb: async function(date) {
				this.reSet(date);

				var endDate = new Date(date);
				endDate.setMonth(date.getMonth() + 1);
				
				this.scatterData = this.getVirtulData(date,endDate);
				const db = uniCloud.database();
				const dbCmd = db.command;
				const res = await db.collection('happyrecord').where({
					// ID:getApp().globalData.ID,
					DateTime: dbCmd.gte(date),
					DateTime: dbCmd.lte(endDate)
				}).get();
				var series = this.getPieSeries(this.scatterData, this.$refs['uni-ec-canvas'].$curChart, res);
				series.push({
					id: 'label',
					type: 'scatter',
					coordinateSystem: 'calendar',
					symbolSize: 1,
					label: {
						normal: {
							show: true,
							formatter: 1,
							offset: [-10, -20],
							fontSize: 12
						}
					},
					data: this.scatterData
				});

				this.ec.option.series = series;
				//console.log(JSON.stringify(this.ec.option));
			},

			getPieSeries: function(scatterData, chart, res) {

				return scatterData.map((item, index) => {
					var center = chart.convertToPixel('calendar', item);
					var happyCount = this.getCount(res.result.data, true, echarts.number.parseDate(item[0]));
					var sadCount = this.getCount(res.result.data, false, echarts.number.parseDate(item[0]));

					return {
						id: index + 'pie',
						type: 'pie',
						center: center,

						label: {

							normal: {
								formatter: '{c}',
								position: 'inside'
							}
						},
						radius: this.pieRadius,
						data: [{
								name: '开心',
								value: Math.round(happyCount)
							},
							{
								name: '伤心',
								value: Math.round(sadCount)
							}
						]
					};
				});
			},

			getPieSeriesUpdate: function(scatterData, chart) {
				return scatterData.map(function(item, index) {
					var center = chart.convertToPixel('calendar', item);
					return {
						id: index + 'pie',
						center: center
					};
				});
			},

		},
		components: {
			uniEcCanvas
		}
	}
</script>

<style>
	page {
		height: 100%;
		width: 100%;
	}

	.content {
		width: 100%;
		height: 100%;
	}

	.uni-padding-wrap {
		background-color: #F0AD4E;
		position: absolute;
		right: 20rpx;
		top: 20rpx;
	}

	.uni-ec-canvas {
		width: 100%;
		height: 100%;
		display: block;
		background-color: #F0AD4E;
	}

	.picker-view {
		width: 750rpx;
		height: 600rpx;
		margin-top: 20rpx;
		background: #FFFFFF;
	}

	.picker {
		position: absolute;
		bottom: 0rpx;

	}

	.item {
		height: 50px;
		align-items: center;
		justify-content: center;
		text-align: center;
	}
</style>
