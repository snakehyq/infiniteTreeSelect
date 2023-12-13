<template>
	<uni-popup ref="popup" type="bottom">
		<view class="tree-list">
			<view class="tree-list-title">
				<text>已选择：{{value.length}}人</text>
				<view class="">
					<text @tap="allClear()" class="all-clear">全部移除</text>
					<text @tap="close()" class="confrim">确认</text>
				</view>
			</view>
			<scroll-view scroll-top="0" scroll-y="true" class="scroll-Y">
				<view class="tree-list-item" v-for="(item,index) in value" :key="index">
					<text>{{item.name}}</text>
					<text class="clear-btn" @tap="treeNodeClear(index)">移除</text>
				</view>
			</scroll-view>	
		</view>
	</uni-popup>
</template>

<script>
	export default {
		name: 'treePopup',
		props: {
			value: {
				type: Array,
				default: () => []
			}
		},
		data() {
			return {
				
			}
		},
		methods: {
			allClear(){
				for (let i = 0; i < this.value.length; i++) {
					const v = this.value[i]
					v.checked = false
					v.halfChecked = false
				}
				this.$emit('input', [])
			},
			treeNodeClear(index){
				this.value[index].checked = false
				this.value[index].halfChecked = false
				this.value.splice(index,1)
			},
			open() {
				this.$refs.popup.open()
			},
			close() {
				this.$refs.popup.close()
			}
		}
	}
</script>

<style scoped >
	.scroll-Y {
		height: calc(60vh);
	}
	.tree-list {
		background-color: #fff;
		border-top-left-radius: 10px;
		border-top-right-radius: 10px;
		padding: 12px 16px;
	}
	.tree-list-title {
		font-weight: 600;
		font-size: 16px;
		display: flex;
		justify-content: space-between;
		padding-top: 12px;
		padding-bottom: 12px;
		border-bottom: 1px solid #e4e1e1;
	}
	.tree-list-title .all-clear {
		color: rgba(0, 0, 0, 0.4);
		cursor: pointer;
		margin-right: 10px;
	}
	.tree-list-title .confrim {
		color: #529edb;
		cursor: pointer;
	}
	.tree-list .tree-list-item {
		padding: 10px 0px;
		border-bottom: 1px solid #e4e1e1;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.clear-btn {
		padding: 5px 12px;
		background-color: #fff;
		border: 1px solid #f2f2f2;
		border-radius: 10px;
	}
</style>