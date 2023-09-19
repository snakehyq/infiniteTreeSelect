<template>
	<view class="tree-node">
			<search></search>
				<scroll-view class="choose-toolbar" scroll-x="true" scroll-left="120">
					<view class="topics-item" v-for="(top,index) in topics" :key="index + 'topics-item'" @tap="handleTopic(index,top)">
						<i v-show="index !== 0" class="iconfont icon-z043 iconclass"></i>
						<text :class="isActive(index) ? 'topics-item-active' : ''">{{top[label]}}</text>
					</view>
				</scroll-view>
			<!-- // 遍历节点渲染列表 -->
			<view class="tree-node-list">
				<view class="node-list-item" v-for="(nodeItem,index) in tree" :key="index + 'node-list-item'" @tap="handleTreeNode(nodeItem,index)">
					<view class="item-left">
						<template v-if="isCheck">
							<!-- // 单选选择 multiple: false-->
							<template v-if="!multiple">
								<view class="select-btn" v-if="!nodes || !nodeItem[children] || !nodeItem[children].length" @tap.stop="handleRadioSelect(nodeItem)">
									<i v-if="isRadioSelect(nodeItem)" class="txt iconfont icon-selected" />
									<i v-else style="color: #b8b8b8;" class="txt iconfont icon-weixuanzhong1" />
								</view>
							</template>
							<!-- 多选选择  multiple： true-->
							<template v-if="multiple">
								<view class="select-btn" @tap.stop="handleMulSelect(nodeItem)">
									<i v-if="isMulSelect(nodeItem) == 1" class="iconfont icon-xuanzhong txt icon-selected" />
									<i v-else-if="isMulSelect(nodeItem) == 2" class="iconfont icon-banxuanzhongshousuo1-shi icons" />
									<i v-else style="color: #b8b8b8;" class="txt iconfont icon-weixuanzhong" />
								</view>
							</template>
						</template>
						<text>{{nodeItem[label]}}</text>
					</view>
					<view class="item-right" v-show="nodeItem[children] && nodeItem[children].length">
						<i class="iconfont icon-z043 iconclass" />
					</view>
				</view>
			</view>
			<view class="footer">
				<view class="footer-left">
					<view class="select">
						已选择：1个
					</view>
					<view class="more-select">最多选择{{number}}个</view>
				</view>
				<view class="footer-right" @tap="handleConfirm">
					完成
				</view>
			</view>
	</view>
</template>

<script>
		import search from '../search/search'
	export default {
		name: 'tree',
		components: {
			search
		},
		props: {
			// 是否选中
			isCheck: Boolean,
			// 数据的唯一标识
			keyCode: {
				type: String,
				default: 'id'
			},
			treeNode: {
				type: Array,
				default: () => []
			},
			number: {
				type:Number,
				default: 1
			},
			// 名字字段label
			label: {
				type: String,
				default: 'name'
			},
			// 子项字段
			children: {
				type: String,
				default:  'children'
			},
			// 是否多选模式
			multiple:Boolean,
			// 单选模式true ,只选子项、false任意一项
			nodes:{
				type: Boolean,
				default: true
			},
			// 是否选中的数据包含路径
			hasPath:Boolean,
			// 是否关联下级
			checkStrictly: Boolean,
			// 回显的数据
			feedBackList: {
				type: Array,
				default: () => []
			}
		},
		computed: {
			isRadioSelect(){
				return (item) => {
					let list = this.selectData
					return list && list.length > 0 && list[0][this.keyCode] == item[this.keyCode]
				}
			},
			isMulSelect(){
				return (item) => {
					let list = this.selectData
					// checked 全选 1  halfChecked 半选择 2  不选中 3
					return item.checked ? 1 : item.halfChecked ? 2 : 3
				}
			},
			// 是否激活
			isActive(){
				return (index)=>{
					return index < this.topics.length - 1
				}
			}
		},
		// watch: {
		// 	// 处理回显的数据
		// 	feedBackList: {
		// 		handler: function(newValue, oldValue) {
		// 			// 根据回显的数据，来回显已选择的数据
		// 			console.log(this.treeNode, this.label,newValue,oldValue);
		// 			// 空数组表示初始化 init()
		// 			if(!newValue.length) return this.init()
		// 			// 存在上个页面选中的数据进行数据回显
		// 			return this.reflexData(newValue[0][this.keyCode])
		// 		},
		// 		deep: true,
		// 		immediate: true
		// 	}
		// },
		created(){
			// 空数组表示初始化 init()
			if(!this.feedBackList.length) return this.init()
			let index = this.feedBackList.length - 1
			// 存在上个页面选中的数据进行数据回显
			this.selectData = JSON.parse(JSON.stringify(this.feedBackList))
			this.reflexData(this.feedBackList[index][this.keyCode])
			
			// const flat = this.toFlatArray(this.treeNode)
			// console.log('扁平化'，flat);
		},
		data(){
			return {
				tree: [],
				// 当前选中的数据
				selectData: [],
				topics: [],
			}
		},
		methods: {
			// 扁平化树形结构
			toFlatArray(tree, parentId = '') {
				  return tree.reduce((t, _) => {
				      const child = _[this.children]
				      return [
				        ...t,
				        parentId ? { ..._, parentId } : _,
				        ...(child && child.length ? this.toFlatArray(child, _[this.keyCode]) : [])]
				    }, [])
			},
			// 回显数据
			 reflexData(key){
				// 根据keyCode找到回显的数据
				// 初始化数据 
				const { label,multiple,treeNode,dfs,handleRadioSelect,children,checkStrictly, handleTreeChecked } = this
				let _topics = [{[label]: '全部', children: treeNode}]
				 let map = []
				 // 通过递归算法找到子节点
				 dfs(treeNode, key, map)
				 _topics.push(...map)
				 // 扁平化树形结构
				 // const flats = this.toFlatArray(treeNode, '0')
				 // 获取所有关联父节点集合
				 // const relativeNode = this.getRelativeNode(flats,key)
				  // _topics.push(...relativeNode)
				 this.topics = _topics
				 this.tree =  Object.freeze(_topics[_topics.length - 1][this.children])
				 checkStrictly && handleTreeChecked()
			 },
			 // 递归遍历找到回显数据 sonNode子节点, key 回显数据的唯一标识，map找到的子节点所有关联父节点集合
			 dfs(sonNode,key,map){
				 for (var i = 0; i < sonNode.length; i++) {
					 const chidNode = sonNode[i]
				 	if(key == chidNode[this.keyCode]) {
						// map.unshift(chidNode)
						console.log('chidNode',chidNode.name);
						return true
					}
					if(chidNode[this.children] && chidNode[this.children].length) {
						const flag = this.dfs(chidNode[this.children],key,map)
						// 本次的父节点添加到map中
						if(flag) {
							
							map.unshift(chidNode)
							return true
						}
					}
				 }
			 },
			 getRelativeNode(flatArray,key){
				 let map = []
				 let child = flatArray.find(_ => _[this.keyCode] == key)
				 while(child && child.parentId) {
					 map.unshift(child)
					 child = flatArray.find(_ => _[this.keyCode] == child.parentId)
				 }
				map.length &&  map.pop()
				 return map
			 },
			// 初始化数据
			init(){
				this.topics = [{[this.label]: '全部', children: this.treeNode}]
				this.tree = Object.freeze(this.treeNode)
			},
			// 完成
			handleConfirm(){
				this.$emit('handleConfirm', this.selectData)
				uni.navigateBack()
			},
			handlePath(){
				if(!this.hasPath) return []
				const map = this.topics.map(_ => {
					const topic = Object.assign({},_)
					delete topic[this.children]
					return topic
				})
				return map.slice(1,map.length) || []
			},
			// 点击tab
			handleTopic(index,topic) {
				console.log(index,topic);
				if(!this.isActive(index)) return
				// 同时删除index后的数据
				this.topics.splice(index+1, this.topics.length)
				this.tree = Object.freeze(topic[this.children])
				this.checkStrictly && this.handleTreeChecked()
			},
			// 点击单选按钮
			handleRadioSelect(item){
				console.log('单选',item);
				// 处理路径
				const path = this.handlePath()
				item.path = path
				this.selectData = [item]
			},
			// 点击多选按钮
			handleMulSelect(item){
				// 选中状态有全选和半选择
				console.log('多选',item,item.checked);
				const {checkStrictly,keyCode,handleSelectRelativeNode,handleCancelRelativeNode,children} = this
				const index = this.selectData.findIndex(_ => _[keyCode] == item[keyCode])
				// 不关联子级
				if(!checkStrictly) {
					item.checked ? this.selectData.splice(index,1) : this.selectData.push(item)
					item.checked = !item.checked
					item.halfChecked = false
					return
				}
				// 关联子级/取消关联子级
				item.checked || item.halfChecked ? handleCancelRelativeNode(item) : this.selectData = this.selectData.concat(handleSelectRelativeNode(item))
				// 全选状态
				item.checked = item.checked || item.halfChecked ? false : true
				item.halfChecked = false
			},
			// 关联子级 
			handleSelectRelativeNode(nodes){
				// 已选中的状态不需要再加进来了 !nodes.checked && !nodes.halfChecked
				let map = []
				!nodes.checked && !nodes.halfChecked &&  map.push(nodes)
				if(nodes[this.children] && nodes[this.children].length) {
					for (var i = 0; i < nodes[this.children].length; i++) {
						const node = nodes[this.children][i]
						if(node[this.children] && node[this.children].length) {
							map = map.concat(this.handleSelectRelativeNode(node))
						} else {
							!nodes.checked && !nodes.halfChecked &&  map.push(node)
						}
						// 设置全选状态
						node.checked = true
						node.halfChecked = false
					}
				}
				return map
			},
			// 取消关联子级
			handleCancelRelativeNode(nodes){
				// 删除selectData中的nodes数据
				if(nodes[this.children] && nodes[this.children].length) {
					for (var i = 0; i < nodes[this.children].length; i++) {
						const node = nodes[this.children][i]
						this.handleCancelRelativeNode(node)
						node.checked = false
						node.halfChecked = false
					}
				}
				const index = this.selectData.findIndex(_ => _[this.keyCode] == nodes[this.keyCode])
				index != -1 && this.selectData.splice(index, 1)
			},
			// 下一级
			handleTreeNode(node,index) {
				// 没有子项不点击
				if(!node[this.children] || !node[this.children].length) return
				// 处理上面的tab
				this.handleTopics(node)
				this.tree = node[this.children]
				// 关联子级
				this.checkStrictly && this.handleTreeChecked()
			},
			// 处理当前节点和子节点的选中与半选择与不选中的状态
			handleTreeChecked(){
				for (var i = 0; i < this.tree.length; i++) {
					const treeItem = this.tree[i]
					// 没有子节点就不需要去改变他的选中状态
					if(!treeItem[this.children] || !treeItem[this.children].length) continue
					// 有子节点去计算出子节点的状态 treeNode子节点的数量  treeNodeSelect选中的子节点的数量
					const {treeNode = 0, treeNodeSelect = 0} = this.handleCalcNodeAccount(treeItem[this.children],0,0)
					// 初始状态0 =0不算  子节点的数量 == 选中的子节点的数量 => 全选状态
					if(treeNode != 0 && treeNodeSelect != 0) {
						// 全选
						if(treeNode == treeNodeSelect) {
							treeItem.checked = true
							treeItem.halfChecked = false
						}
						//半选
						else{
							treeItem.checked = false
							treeItem.halfChecked = true
						}
					} else {
						treeItem.checked = false
						treeItem.halfChecked = false
					}
				}
			},
			handleCalcNodeAccount(children,treeNode,treeNodeSelect){
				for (var i = 0; i < children.length; i++) {
					const child = children[i]
					if(child[this.children] && child[this.children].length) {
						const treeAccount = this.handleCalcNodeAccount(child[this.children],treeNode,treeNodeSelect)
						treeNode = treeAccount.treeNode
						treeNodeSelect = treeAccount.treeNodeSelect
					}
					treeNode++
					// 判断节点是否存在于selectData已选择的数据中，如果存在表示选中，不存在表示不选中
					const findIndex =  this.selectData.findIndex(_ => _[this.keyCode] == child[this.keyCode])
					console.log(child[this.keyCode],findIndex);
					findIndex != -1 &&  treeNodeSelect++
				}
				return {
					treeNode,
					treeNodeSelect
				}
			},
			// 处理上面的tab
			handleTopics(node){
				// tabs 列表下标赋值 
				// 添加到tab列表中
				this.topics.push(node)
			}
		}
	}
</script>

<style scoped lang="scss">
	@import '../css/style.scss';
	@import url("../css/icon.css");
</style>