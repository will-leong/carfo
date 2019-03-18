<template>
	<view>
		<view class="uni-flex uni-row grace-bg-white" style="padding: 20upx;margin-bottom: 20upx;">
			<view class="uni-flex my-flex-ccl uni-column" style="flex: 0 0 100upx;">
				<text>今天</text>
				<text class="grace-h4" style="font-weight: bold;">8:00</text>
			</view>
			<view class="uni-flex uni-column" style="flex: 5;padding-left: 10upx;">
				<view class="uni-flex my-flex-rcl">
					<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
					<text class="grace-h5 in-line-padding">宅梧镇</text>
				</view>
				<view class="uni-flex my-flex-rcl">
					<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
					<text class="grace-h5 in-line-padding">鹤山汽车总站</text>
				</view>
				<view class="uni-flex my-flex-rcl">
					<uni-icon type="info" size="14" color="#2c2c2c"></uni-icon>
					<text class="uni-text-small in-line-padding" style="color: #C0C0C0;">约45.12km 估计40元</text>
				</view>
			</view>
			<view class="uni-flex my-flex-rcc" style="flex: 0 0 120upx;padding: 40upx 0;">
				<text style="color: #C0C0C0;">取消行程</text>
			</view>
		</view>
		<view class="uni-flex" style="justify-content: center;align-items: center;">
			<view class="grace-h4" style="font-weight: bold;">匹配的行程</view>
		</view>
		<view class="order-match">
			<view v-for="i in [1,2,3,4,5,1,2,3,4,5]" :key='i' class="uni-flex uni-row grace-bg-white box-shadow box-radius" style="padding: 20upx;margin-bottom: 20upx;">
				<view class="uni-flex my-flex-ccl uni-column" style="flex: 0 0 100upx;">
					<text>今天</text>
					<text class="grace-h4" style="font-weight: bold;">9:00</text>
				</view>
				<view class="uni-flex uni-column" style="flex: 5;padding-left: 10upx;">
					<view class="uni-flex my-flex-rcl">
						<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
						<text class="grace-h5 in-line-padding">鹤城镇</text>
					</view>
					<view class="uni-flex my-flex-rcl">
						<uni-icon type="circle-filled" size="14" color="#09BB07"></uni-icon>
						<text class="grace-h5 in-line-padding">鹤山汽车总站</text>
					</view>
					<view class="uni-flex my-flex-rcl">
						<uni-icon type="spinner" size="14" color="#2c2c2c"></uni-icon>
						<text class="uni-text-small in-line-padding" style="color: #C0C0C0;">匹配度 85%</text>
					</view>
				</view>
				<view class="uni-flex my-flex-rcc" style="flex: 0 0 120upx;">
					<text style="color: #C0C0C0;">获取联系</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniIcon from "../../components/uni-icon/uni-icon.vue"
	
	export default {
		components: {
			uniIcon
		},
		
		data() {
			return {
				
			};
		}
	}
</script>

<style>
	.order-match{
		padding: 20upx;
	}
</style>
