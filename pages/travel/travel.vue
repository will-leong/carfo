<template>
	<view>
		<view class="uni-padding-wrap grace-bg-white">
			<uni-segmented-control :current="current" :values="items" v-on:clickItem="onClickItem" :styleType="styleType"
			 :activeColor="activeColor"></uni-segmented-control>
		</view>
	</view>
</template>

<script>
	import uniSegmentedControl from '../../components/uni-segmented-control/uni-segmented-control.vue';
	
	export default {
		components: {
			uniSegmentedControl
		},
		data() {
			return {
				styleType: 'text',
				current: 0,
				items: [
					'进行中',
					'发布中'
				],
			};
		}
	}
</script>

<style>

</style>
