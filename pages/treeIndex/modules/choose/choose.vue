<template>
	<view>
		<hyq-tree-vtw v-bind="prop" :tree-node="treeNode" :feed-back-list="feedBackList" is-check show-search key-code="id" @handleConfirm="handleConfirm" ></hyq-tree-vtw>
	</view>
</template>

<script>
	import {treeNode} from './data.js'
	import hyqTreeVtw from '../tree/tree'

	export default {
		name: 'choose',
		components: {
			hyqTreeVtw
		},
		data(){
			return {
				treeNode,
				prop: {},
				feedBackList: []
			}
		},
		onLoad(options){
			// #ifdef H5
				let prop = JSON.parse(decodeURIComponent(options.prop));
				let feedBackList = JSON.parse(decodeURIComponent(options.data));
			// #endif
			// #ifdef MP-QQ||MP-WEIXIN
				let prop = JSON.parse(options.prop);
				let feedBackList = JSON.parse(options.data);
			// #endif
			this.prop = prop
			this.feedBackList = feedBackList
		},
		methods: {
			handleConfirm(val) {
				console.log('val',val);
				// 获取上一个页面
				var pages = getCurrentPages(); //当前页面栈
				var beforePage = pages[pages.length - 2]; //获取上一个页面实例对象
				beforePage.$vm.setConfirmData(val); //触发上一个页面中的update方法
			}
		}
	}
</script>

<style scoped>
</style>