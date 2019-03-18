<template>
	<view>
		<view class="uni-padding-wrap uni-common-mt">
			<uni-segmented-control :current="current" :values="items" v-on:clickItem="onClickItem" :styleType="styleType"
			 :activeColor="activeColor"></uni-segmented-control>
		</view>
		<view class="content">
			<view v-show="current === 0">
				<view v-for="order in passenger.orders" :key="order.index" class="grace-bg-white box-shadow" style="margin: 35upx;border-radius: 20upx;overflow: hidden;">
					<navigator url="../orderDetail/orderDetail">
					<view class="uni-flex uni-row" style="justify-content: space-between;">
						<view class="uni-flex uni-column" style="flex: 7;padding-bottom: 20upx;">
							<view class="uni-flex" style="align-items: flex-start;">
								<text size="small" style="border-radius: 20upx 0;background: #09BB07;font-size: 22upx;color: #FFFFFF;padding: 0 20upx;">{{order.status}}</text>
							</view>
							<view class="uni-flex uni-row">
								<view class="uni-flex uni-row my-flex-rcc" style="flex: 0 0 200upx;">
									
									<text class="grace-h3" style="font-weight: bold;">{{order.planTime.time}}
										<text class="grace-text-small">{{order.planTime.date}}</text>
									</text>
								</view>
								<view class="uni-flex uni-column" style="flex: 4;">
									<view>
										<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
										<text class="grace-h5 in-line-padding">{{order.originLocation}}</text>
									</view>
									<view>
										<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
										<text class="grace-h5 in-line-padding">{{order.destinationLocation}}</text>
									</view>
								</view>
								<!-- <view class="uni-flex uni-column" style="flex: 0 0 150upx;padding-left: 20upx;">
									<view class="uni-flex grace-text-small">
										<text style="padding-left: 10upx;color: #808080;">约{{order.approximateDistance}}</text>
									</view>
									<view class="uni-flex grace-text-small">
										<text style="padding-left: 10upx;color: #808080;">预计{{order.estimatedPrice}}</text>
									</view>
								</view> -->
							</view>
						</view>
						<view class="uni-flex my-flex-rcc" style="flex: 0 0 100upx;">
							<view>
								<uni-icon type="forward" size="28" color="#2c2c2c"></uni-icon>
							</view>
						</view>
					</view>
					</navigator>
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
									<view class="uni-flex my-flex-rcl"><text>{{passenger.planTime.date+' '+passenger.planTime.time}}</text></view>
								</view>
								<view class="grace-items" v-if="passenger.showMessage">
									<view class="grace-label">大概里程</view>
									<input type="text" class="input" v-model="passenger.approximateDistance" disabled="true"></input>
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
					 @onConfirm="onPickerConfirm($event,passenger)" :pickerValueArray="pickerValueArray"></mpvue-picker>
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
	import Vue from 'vue'
	
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

				passengerInit: {
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
					planTime: {
						date:'',
						time:''
					},
					approximateDistance: '',
					estimatedPrice: '',
					showMessage: false
				},
				passenger: {},
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
		onLoad() {
			this.passenger = this.copyObject(this.passengerInit)
			this.passenger['orders'] = [{
				originLocation: '宅梧镇',
				destinationLocation: '鹤山汽车总站',
				planTime: {
					date:'今天',
					time: '8:00'
				},
				approximateDistance: '45.23km',
				estimatedPrice: '41元',
				status: '行程匹配中'
			}]
		},
		methods: {
			copyObject(obj) {
				var copyObj = new Object()
				for (var item in obj) {
					if (obj[item] instanceof Object)
						copyObj[item] = this.copyObject(obj[item])
					else{
						copyObj[item] = obj[item]
					}
				}
				return copyObj
			},
			setObject(objSet,objInit){
				for (var item in objInit) {
					if (objInit[item] instanceof Object)
						this.setObject(objSet[item],objInit[item])
					else{
						Vue.set(objSet,item,objInit[item])
					}
				}
			},
			chooseLocation(obj) {
				console.log(obj)
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
			formSubmit() {
				console.log(this.passenger)
				var order = {
					originLocation: this.passenger.origin.location,
					destinationLocation: this.passenger.destination.location,
					planTime: this.passenger.planTime.replace('今天-',''),
					approximateDistance: this.passenger.approximateDistance,
					estimatedPrice: this.passenger.estimatedPrice,
					status: '行程匹配中'
				}
				this.passenger.orders.push(order)
				this.setObject(this.passenger,this.passengerInit)
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
						var labels = e.label.split('-')
						obj.planTime.date = labels[0]
						obj.planTime.time = labels[1]+':'+labels[2]
					}
				}
			},
			getapproximateDistance(obj) {
				if (obj.origin.latitude !== 0 && obj.destination.latitude !== 0) {
					console.log(obj)
					obj.showMessage = true;
					obj.approximateDistance = '计算中...'
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
							obj.approximateDistance = (res.result.routes[0].distance / 1000).toFixed(2).toString() + 'km'
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
			passengerapproximateDistance() {
				return this.getapproximateDistance(this.passenger)
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
</style>
