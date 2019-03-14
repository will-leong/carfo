<template>
	<view class="release-page">
		<view>
			<view class="grace-form">
				<form @submit="formSubmit">
					<view class="release-content grace-bg-white">
						<view class="grace-items" @click="chooseOriginLocation()">
							<view class="grace-label">出发地</view>
							<input type="text" class="input" v-model="origin_location" disabled="true" placeholder="点击选择出发地"></input>
						</view>
						<view class="grace-items" @click="chooseDestinationLocation()">
							<view class="grace-label">目的地</view>
							<input type="text" class="input" v-model="destination_location" disabled="true" placeholder="点击选择目的地"></input>
						</view>
						<view class="grace-items">
							<view class="grace-label">乘坐人数</view>
							<view class="grace-form-r">
								<picker @change="bindPickerChange" :value="passengerNumIndex" :range="passengerNum" name="passengerNum">
									<text>{{passengerNum[passengerNumIndex]}}</text>
								</picker>
							</view>
						</view>
						<view class="grace-items" @click="showTimePicker">
							<view class="grace-label">计划时间</view>
							<input type="text" class="input" v-model="planTime" disabled="true"></input>
						</view>
						<view class="grace-items">
							<view class="grace-label">大概里程</view>
							<input type="text" class="input" v-model="approximateDistance" disabled="true"></input>
						</view>
						<view class="grace-items grace-noborder">
							<view class="grace-label">预计价格</view>
							<input type="text" class="input" v-model="estimatedPrice" disabled="true"></input>
						</view>
					</view>
					<view>
						<button formType="submit" type="primary" style="width:100%;border-radius: 0 0 20upx 20upx;background: #2c2c2c;">release</button>
					</view>
				</form>
			</view>
		</view>
		<mpvue-picker :themeColor="themeColor" ref="mpvuePicker" :mode="mode" :deepLength="deepLength" :pickerValueDefault="timePickerValueDefault"
		 @onConfirm="onTimePickerConfirm" @onCancel="onCancel" :pickerValueArray="pickerValueArray"></mpvue-picker>
	</view>
</template>

<script>
	// https://github.com/zhetengbiji/mpvue-picker
	import mpvuePicker from '../../components/mpvue-picker/mpvuePicker.vue';
	import timeData from '../../common/time.data.js';
	import QQMapWX from '../../common/qqmap-wx-jssdk.min.js'

	var qqmapsdk = new QQMapWX({
		key: 'S6PBZ-76735-PTZIT-Q27QH-LKQQO-FQBTW'
	})

	export default {
		components: {
			mpvuePicker
		},
		data() {
			return {
				origin_location: "",
				origin_longitude: 0,
				origin_latitude: 0,

				destination_location: "",
				destination_longitude: 0,
				destination_latitude: 0,

				passengerNumIndex: 0,
				passengerNum: [1, 2, 3, 4],
				planTime: "",
				pickerValueArray: timeData,
				timePickerValueDefault: [],
				themeColor: '#007AFF',
				mode: 'multiSelector',
				deepLength: 3,

				approximateDistance: '',
				estimatedPrice: ''
			}
		},
		methods: {
			chooseOriginLocation: function() {
				var app = this
				uni.chooseLocation({
					success: function(res) {
						app.origin_location = res.name
						app.origin_latitude = res.latitude
						app.origin_longitude = res.longitude
					}
				});
			},
			chooseDestinationLocation: function() {
				var app = this
				uni.chooseLocation({
					success: function(res) {
						app.destination_location = res.name
						app.destination_latitude = res.latitude
						app.destination_longitude = res.longitude
					}
				});
			},
			bindPickerChange: function(e) {
				console.log(e);
				this.passengerNumIndex = e.detail.value;
			},
			bindDateChange: function(e) {
				this.dateValue = e.detail.value;
			},
			formSubmit: function(e) {
				wx.showToast({
					title: '请观察控制台',
					icon: 'none'
				});
				console.log(JSON.stringify(e.detail.value));
			},
			showTimePicker() {
				var date = new Date()
				var hour = date.getHours()
				var minute = date.getMinutes()
				this.timePickerValueDefault = [0, hour, minute]
				this.$refs.mpvuePicker.show()
			},
			onTimePickerConfirm(e) {
				console.log(e)
				var date = new Date()
				var hour = date.getHours()
				var minute = date.getMinutes()
				if (e.index[0] === 0 && (e.index[1] < hour || e.index[1] >= hour && e.index[2] < minute)) {
					wx.showToast({
						title: '不能小于当前时间',
						icon: 'none'
					});
				} else {
					this.planTime = e.label
				}
			},
			onChangeLocation() {
				var app = this
				if (this.origin_latitude !== 0 && this.destination_latitude !== 0) {
					console.log('mapsdk')
					qqmapsdk.direction({
						mode: 'driving',
						from: {
							latitude: app.origin_latitude,
							longitude: app.origin_longitude
						},
						to: {
							latitude: app.destination_latitude,
							longitude: app.destination_longitude
						},
						success: function(res) {
							console.log(res)
							app.approximateDistance = res.result.routes[0].distance
						},
						fail: function(error) {
							console.error(error);
						},
						complete: function(res) {
							console.log(res);
						}
					})
				}
			}
		},
		watch: {
			origin_latitude: function() {
				console.log(this.approximateDistance)
				this.onChangeLocation()
			},
			destination_latitude: function() {
				console.log(this.approximateDistance)
				this.onChangeLocation()
			}
		}
	}
</script>

<style>
	.release-page {
		padding: 35upx;
	}

	.release-content {
		padding: 20upx 35upx;
		border-radius: 20upx 20upx 0 0;
	}
	.grace-label{
		width:150upx;
	}
	.grace-label,input {
		font-size: 32upx !important;
	}
</style>
