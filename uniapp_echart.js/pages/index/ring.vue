<template>
	<view class="ring">
		<view class="qiun-charts">
			<!--#ifdef MP-ALIPAY -->
			<canvas canvas-id="canvasRing" id="canvasRing" class="charts" :style="{'width':cWidth*pixelRatio+'px','height':cHeight*pixelRatio+'px', 'transform': 'scale('+(1/pixelRatio)+')','margin-left':-cWidth*(pixelRatio-1)/2+'px','margin-top':-cHeight*(pixelRatio-1)/2+'px'}"
			 @touchstart="touchRing"></canvas>
			<!--#endif-->
			<textarea placeholder="" />
			<!--#ifdef H5 -->
			<canvas canvas-id="canvasRing" id="canvasRing" class="charts" @touchstart="touchRing"></canvas>
			<!--#endif-->
		</view>
	</view>
</template>

<script>
	import uCharts from '@/components/u-charts/u-charts.js';

	var _self;
	var canvaRing = null;

	export default {
		data() {
			return {
				cWidth: '',
				cHeight: '',
				pixelRatio: 1,
				textarea: {
					series: [{
							name: "一班",
							data: 50
						},
						{
							name: "二班",
							data: 10
						},
						{
							name: "三班",
							data: 100
						},
						{
							name: "四班",
							data: 26
						},
						{
							name: "五班",
							data: 40
						},
						{
							name: "六班",
							data: 2
						}
					]
				}
			}
		},
		created() {
			_self = this;
			//#ifdef MP-ALIPAY
			uni.getSystemInfo({
				success: function(res) {
					if (res.pixelRatio > 1) {
						//正常这里给2就行，如果pixelRatio=3性能会降低一点
						//_self.pixelRatio =res.pixelRatio;
						_self.pixelRatio = 2;
					}
				}
			});
			//#endif
			this.cWidth = uni.upx2px(750);
			this.cHeight = uni.upx2px(500);
			this.showRing('canvasRing',this.textarea);
		},
		methods: {
			showRing(canvasId, chartData) {
				console.log('=======',chartData)
				canvaRing = new uCharts({
					$this: _self,
					canvasId: canvasId,
					type: 'ring',
					fontSize: 11,
					padding: [5, 5, 5, 5],
					legend: {
						show: true,
						position: 'right',
						float: 'center',
						itemGap: 10,
						padding: 5,
						lineHeight: 26,
						margin: 5,
						borderWidth: 1
					},
					background: '#FFFFFF',
					pixelRatio: _self.pixelRatio,
					series: chartData.series,
					animation: false,
					width: _self.cWidth * _self.pixelRatio,
					height: _self.cHeight * _self.pixelRatio,
					disablePieStroke: true,
					dataLabel: true,
					subtitle: {
						name: '70%',
						color: '#7cb5ec',
						fontSize: 25 * _self.pixelRatio,
					},
					title: {
						name: '收益率',
						color: '#666666',
						fontSize: 15 * _self.pixelRatio,
					},
					extra: {
						pie: {
							offsetAngle: 0,
							ringWidth: 40 * _self.pixelRatio,
							labelWidth: 15
						}
					},
				});
			},
			touchRing(e) {
				canvaRing.touchLegend(e, {
					animation: false
				});
				canvaRing.showToolTip(e, {
					format: function(item) {
						return item.name + ':' + item.data
					}
				});
			},
			changeData() {
				if (_self.textarea) {
					let newdata = JSON.parse(_self.textarea);
					canvaRing.updateData({
						series: newdata.series,
						categories: newdata.categories
					});
				} else {
					uni.showToast({
						title: '数据格式错误',
						image: '../../../static/images/alert-warning.png'
					})
				}
			}
		}
	}
</script>

<style scoped lang="scss">
	.ring{
		width: 100%;
	height: 200px;
	background-color: #DD524D;
	}
	/*样式的width和height一定要与定义的cWidth和cHeight相对应*/
	.qiun-charts {
		width: 750upx;
		height: 500upx;
		background-color: #FFFFFF;
	}

	.charts {
		width: 750upx;
		height: 500upx;
		background-color: #FFFFFF;
	}
</style>
