<template>
	<view>
		<view class="uni-padding-wrap uni-common-mt">
			<uni-segmented-control :current="current" :values="items" v-on:clickItem="onClickItem" :styleType="styleType"
			 :activeColor="activeColor"></uni-segmented-control>
		</view>
		<view class="content">
			<view v-show="current === 0">
				<view class="grace-bg-white box-shadow" style="margin: 35upx;border-radius: 20upx;overflow: hidden;">
					<view class="uni-flex uni-row" style="justify-content: space-between;">
						<view class="uni-flex-item uni-flex uni-column" style="flex: 4;">
							<view class="uni-flex uni-row" style="align-items: flex-start;justify-content: space-between;">
								<view class="uni-flex tag-view" style="align-items: flex-start;">
									<uni-tag text="行程匹配中" type="success" size="small" style="border-radius: 20upx 0;"></uni-tag>
								</view>
								<!-- <view>
									<text>今天 12：00</text>
								</view> -->
							</view>
							<view class="uni-flex-item" style="padding: 0 20upx; display: flex;justify-content: flex-start;align-items: center;">
								<uni-icon type="circle" size="10" color="#2c2c2c"></uni-icon>
								<text style="padding-left: 10upx;">宅梧镇</text>
							</view>
							<view class="uni-flex-item" style="padding: 0 20upx 35upx; display: flex;justify-content: flex-start;align-items: center;">
								<uni-icon type="circle" size="10" color="#2c2c2c"></uni-icon>
								<text style="padding-left: 10upx;">鹤山汽车总站</text>
							</view>
						</view>
						<view class="uni-flex-item" style="flex: 1;display: flex;justify-content: center;align-items: center;">
							
							<view>
								<uni-icon type="forward" size="28" color="#2c2c2c"></uni-icon>
							</view>
						</view>
					</view>
				</view>
				<view class="release-page">
					<view class="grace-form box-shadow">
						<form @submit="formSubmit">
							<view class="release-content grace-bg-white">
								<view class="grace-items" @click="chooseLocation(passenger.origin)">
									<view class="grace-label">出发地</view>
									<input type="text" class="input" v-model="passenger.origin.location" disabled="true" placeholder="点击选择出发地"></input>
								</view>
								<view class="grace-items" @click="chooseLocation(passenger.destination)">
									<view class="grace-label">目的地</view>
									<input type="text" class="input" v-model="passenger.destination.location" disabled="true" placeholder="点击选择目的地"></input>
								</view>
								<view class="grace-items" @click="showSinglePicker()">
									<view class="grace-label">乘坐人数</view>
									<input type="text" class="input" v-model="passenger.nums" disabled="true"></input>
								</view>
								<view class="grace-items" @click="showMulPicker()">
									<view class="grace-label">计划时间</view>
									<input type="text" class="input" v-model="passenger.planTime" disabled="true"></input>
								</view>
								<view class="grace-items" v-if="passenger.showMessage">
									<view class="grace-label">大概里程</view>
									<input type="text" class="input" v-model="passenger.distance" disabled="true"></input>
								</view>
								<view class="grace-items grace-noborder" v-if="passenger.showMessage">
									<view class="grace-label">预计价格</view>
									<input type="text" class="input" v-model="passenger.estimatedPrice" disabled="true"></input>
								</view>
							</view>
							<view>
								<button formType="submit" type="primary" style="width:100%;border-radius: 0 0 20upx 20upx;background: #2c2c2c;">release</button>
							</view>
						</form>
					</view>

					<mpvue-picker :themeColor="themeColor" ref="mpvuePicker" :mode="mode" :deepLength="deepLength" :pickerValueDefault="pickerValueDefault"
					 @onConfirm="onPickerConfirm($event,passenger)" @onCancel="onCancel" :pickerValueArray="pickerValueArray"></mpvue-picker>
				</view>
			</view>
			<view v-show="current === 1">
			</view>
		</view>
	</view>

</template>

<script>
	// https://github.com/zhetengbiji/mpvue-picker
	import mpvuePicker from '../../components/mpvue-picker/mpvuePicker.vue';
	import timeData from '../../common/time.data.js';
	import QQMapWX from '../../common/qqmap-wx-jssdk.min.js'
	import uniSegmentedControl from '../../components/uni-segmented-control/uni-segmented-control.vue';
	import uniIcon from "../../components/uni-icon/uni-icon.vue"
	import uniTag from "../../components/uni-tag/uni-tag.vue"

	var qqmapsdk = new QQMapWX({
		key: 'S6PBZ-76735-PTZIT-Q27QH-LKQQO-FQBTW'
	})

	export default {
		components: {
			mpvuePicker,
			uniSegmentedControl,
			uniIcon,
			uniTag
		},
		data() {
			return {
				current: 0,
				items: [
					'乘客',
					'司机'
				],
				styleType: 'button',
				activeColor: '#2c2c2c',

				passenger: {
					origin: {
						location: "",
						longitude: 0,
						latitude: 0,
					},
					destination: {
						location: "",
						longitude: 0,
						latitude: 0,
					},
					nums: 1,
					planTime: "",
					distance: '',
					estimatedPrice: '',
					
					showMessage: false
				},

				numsArray: [{
					label: 1,
					value: 1
				}, {
					label: 2,
					value: 2
				}, {
					label: 3,
					value: 3
				}, {
					label: 4,
					value: 4
				}],
				isSingle: true,
				pickerValueArray: [],
				pickerValueDefault: [],
				themeColor: '#2c2c2c',
				mode: '',
				deepLength: 3
			}
		},
		methods: {
			chooseLocation(obj) {
				wx.showLoading({
				  title: '加载中',
				})
				uni.chooseLocation({
					success(res) {
						obj.location = res.name
						obj.latitude = res.latitude
						obj.longitude = res.longitude
					},
					complete() {
						wx.hideLoading()
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
					icon: 'loading'
				});
				console.log(JSON.stringify(e.detail.value));
			},
			showSinglePicker() {
				this.isSingle = true
				this.mode = 'selector'
				this.pickerValueArray = this.numsArray
				this.pickerValueDefault = [0]
				this.$refs.mpvuePicker.show()
			},
			showMulPicker() {
				this.isSingle = false
				this.pickerValueArray = timeData
				this.mode = 'multiSelector'
				var date = new Date()
				var hour = date.getHours()
				var minute = date.getMinutes()
				this.pickerValueDefault = [0, hour, minute]
				this.$refs.mpvuePicker.show()
			},
			onPickerConfirm(e, obj) {
				console.log(e)
				if (this.isSingle) {
					obj.nums = e.label
				} else {
					var date = new Date()
					var hour = date.getHours()
					var minute = date.getMinutes()
					if (e.index[0] === 0 && (e.index[1] < hour || e.index[1] === hour && e.index[2] < minute)) {
						wx.showToast({
							title: '不能小于当前时间',
							icon: 'none'
						});
					} else {
						obj.planTime = e.label
					}
				}
			},
			getDistance(obj) {
				if (obj.origin.latitude !== 0 && obj.destination.latitude !== 0) {
					console.log(obj)
					obj.showMessage = true;
					obj.distance = '计算中...'
					obj.estimatedPrice = '计算中...'
					qqmapsdk.direction({
						mode: 'driving',
						from: {
							latitude: obj.origin.latitude,
							longitude: obj.origin.longitude
						},
						to: {
							latitude: obj.destination.latitude,
							longitude: obj.destination.longitude
						},
						success: function(res) {
							console.log(res)
							obj.distance = (res.result.routes[0].distance / 1000).toFixed(2).toString() + 'km'
							obj.estimatedPrice = (res.result.routes[0].distance / 1000 * 0.85).toFixed(2) + '元'
						},
						fail: function(error) {
							console.error(error);
						},
						complete: function(res) {
							console.log("qqmap complete");
						}
					})
				}
			},
			onClickItem(index) {
				if (this.current !== index) {
					this.current = index;
				}
			},
		},
		computed: {
			passengerDistance() {
				return this.getDistance(this.passenger)
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

	.grace-label {
		width: 150upx;
	}

	.grace-label,
	input {
		font-size: 32upx !important;
	}
	.box-shadow{
		box-shadow: 0 0 50upx #e1e1e1;
	}
</style>
