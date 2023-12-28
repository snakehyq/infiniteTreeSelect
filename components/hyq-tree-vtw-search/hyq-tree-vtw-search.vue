<template>
	<view class="search-tree">
		<view class="search-box">
			<input class="uni-input" v-model="inputValue" confirm-type="search" placeholder="搜索" @confirm="searchConfirm" />
		</view>
	</view>
</template>

<script>
	export default {
		name: "treeSearch",
		data() {
			return {
				inputValue: ''
			}
		},
		methods: {
			searchConfirm(){
				this.$emit('confirm', this.inputValue)
			},
			clear(){
				console.log('清空了');
				this.inputValue = ''
			}
		}
	}
</script>

<style scoped>
	.search-tree {
		padding: 8px 20px;
		background-color: #ffffff;
	}
	.search-box {
		background-color: #eeeff0;
		border-radius: 20px;
		padding: 5px 20px;
		font-size: 10px;
	}
</style>