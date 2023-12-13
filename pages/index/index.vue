<template>
	<view class="content">
		<hyq-tree-vtw :label="dprop.label" :children="dprop.children" :key-code="dprop.keyCode"
			:has-path="dprop.hasPath" :nodes="dprop.nodes" :multiple="dprop.multiple"
			:checkStrictly="dprop.checkStrictly" :tree-node="treeNode" :feed-back-list="feedBackList" is-check
			show-search @handleConfirm="handleConfirm"></hyq-tree-vtw>
	</view>
</template>

<script>
	import {
		treeNode
	} from './data.js'
	export default {
		data() {
			return {
				treeNode,
				feedBackList: [],
				aprop: {
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: true,
					hasPath: false
				},
				bprop: {
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: true,
					checkStrictly: true,
					hasPath: false
				},
				cprop: { //单选模式(任意一项)
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: false,
					nodes: false,
					hasPath: false
				},
				dprop: { //单选模式选user
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: false,
					nodes: true,
					hasPath: false
				}
			}
		},
		onLoad() {

		},
		methods: {
			handleConfirm(val) {
				console.log('val', val);
				// 获取上一个页面
				var pages = getCurrentPages(); //当前页面栈
				var beforePage = pages[pages.length - 2]; //获取上一个页面实例对象
				beforePage.$vm.setConfirmData(val); //触发上一个页面中的update方法
			}
		}
	}
</script>

<style>
</style>