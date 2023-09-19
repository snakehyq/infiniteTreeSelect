<template>
	<view class="tree-index">
		<view class="tree-index-title">无限级组件-动态加载-me</view>
		<button type="primary" @tap="handleChooseTree(aprop)">多选模式（选择任意一项）</button>
		<button type="primary" @tap="handleChooseTree(bprop)">多选模式（关联下级）</button>
		<button type="primary"  @tap="handleChooseTree(cprop)">单选模式（选择任意一项）</button>
		<button type="primary" @tap="handleChooseTree(dprop)">单选模式（只选user）</button>
		<button @tap="handleClear">清空选择</button>
		<view v-for="(item,index) in selectData">
			<view>名字： {{item.name}}</view>
			<view>id： {{item.id}}</view>
			<view v-show="item.path.length">
				path： <text v-for="(pa,index) in item.path">{{pa.name + ' '}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	
	export default {
		name:"treeIndex",
		data(){
			return {
				selectData: [],
				aprop: {
					label: 'name',
					children: 'children',
					multiple:true,
					hasPath:false
				},
				bprop: {
					label: 'name',
					children: 'children',
					multiple:true,
					checkStrictly:true,
					hasPath:false
				},
				cprop: {//单选模式(任意一项)
					label: 'name',
					children: 'children',
					multiple:false,
					nodes:false,
					hasPath:false
				},
				dprop: {//单选模式选user
					label: 'name',
					children: 'children',
					multiple:false,
					nodes:true,
					hasPath:false
				}
			}
		},
		methods: {
			handleClear(){
				this.selectData = []
			},
			handleChooseTree(props){
				// #ifdef H5
					let prop = encodeURIComponent(JSON.stringify(props));
					let data = encodeURIComponent(JSON.stringify(this.selectData));
				// #endif
				// #ifdef MP-QQ||MP-WEIXIN
					let prop = JSON.stringify(props);
					let data = JSON.stringify(this.selectData);
				// #endif
				uni.navigateTo({
					url: "/pages/treeIndex/modules/choose/choose?prop="+ prop + '&data=' + data
				})
			},
			setConfirmData(data) {
				this.selectData = data
			}
		}
	}
</script>

<style scoped>
	.tree-index-title {
		text-align: center;
		font-weight: bold;
		font-size: 16px;
		line-height: 45px;
	}
	.tree-index > button {
		margin-top: 10px;
	}
</style>