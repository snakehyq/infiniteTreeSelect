<template>
	<view class="tree-node">
		<hyq-tree-search ref="searchRef" v-show="showSearch" @confirm="confirmSearch"></hyq-tree-search>
		<scroll-view class="choose-toolbar" scroll-x="true" scroll-left="120">
			<view class="topics-item" v-for="(top, index) in topics" :key="index" @tap="handleTopic(index, top)">
				<i v-if="index !== 0" class="iconfont icon-z043 iconclass"></i>
				<text :class="isActive(index) ? 'topics-item-active' : ''">{{
          top[label]
        }}</text>
			</view>
		</scroll-view>
		<scroll-view scroll-y ref="infiniteList" class="infinite-list-container" :style="{height: infinityHeight}"
			@scroll="scrollEvent($event)">
			<view class="infinite-list-phantom" :style="{ height: listHeight + 'px' }"></view>
			<!-- // 遍历节点渲染列表 -->
			<view class="tree-node-list" ref="nodeListRef" :style="{ transform: getTransform }">
				<view class="node-list-item" v-for="(nodeItem, index) in visibleData" :key="nodeItem[keyCode]"
					@tap="handleTreeNode(nodeItem, index)" :data-index="index">
					<view class="item-left">
						<template v-if="isCheck">
							<!-- // 单选选择 multiple: false-->
							<template v-if="!multiple">
								<view class="select-btn" v-if="
                  !nodes || !nodeItem[children] || !nodeItem[children].length
                " @tap.stop="handleRadioSelect(nodeItem)">
									<i v-if="isRadioSelect(nodeItem)" class="txt iconfont icon-selected" />
									<i v-else style="color: #b8b8b8" class="txt iconfont icon-weixuanzhong1" />
								</view>
							</template>
							<!-- 多选选择  multiple： true-->
							<template v-if="multiple">
								<view class="select-btn" @tap.stop="handleMulSelect(nodeItem)">
									<i v-if="isMulSelect(nodeItem) == 1"
										class="iconfont icon-xuanzhong txt icon-selected" />
									<i v-else-if="isMulSelect(nodeItem) == 2"
										class="iconfont icon-banxuanzhongshousuo1-shi icons" />
									<i v-else style="color: #b8b8b8" class="txt iconfont icon-weixuanzhong" />
								</view>
							</template>
						</template>
						<text>{{ nodeItem[label] }}</text>
					</view>
					<view class="item-right" v-if="nodeItem[children] && nodeItem[children].length">
						<i class="iconfont icon-z043 iconclass" />
					</view>
				</view>
			</view>
		</scroll-view>
		<view class="footer">
			<view class="footer-left">
				<view class="select">
					已选择：
					<text v-if="selectData.length" @tap="selectOpen">
						{{ selectData.length }}个
						<text class="select-row">^</text>
					</text>
				</view>
				<!-- <view class="more-select">最多选择{{number}}个</view> -->
			</view>
			<view class="footer-right" @tap="handleConfirm"> 完成 </view>
		</view>
		<hyq-tree-popup ref="treePopup" v-model="selectData"></hyq-tree-popup>
	</view>
</template>

<script>
	import hyqTreeSearch from "@/components/hyq-tree-search/hyq-tree-search.vue";
	import hyqTreePopup from '@/components/hyq-tree-popup/hyq-tree-popup.vue'
	export default {
		name: "tree",
		components: {
			hyqTreeSearch,
			hyqTreePopup
		},
		props: {
			// 是否选中
			isCheck: Boolean,
			showSearch: Boolean,
			// 数据的唯一标识
			keyCode: {
				type: String,
				default: "id",
			},
			treeNode: {
				type: Array,
				default: () => [],
			},
			// 名字字段label
			label: {
				type: String,
				default: "name",
			},
			// 子项字段
			children: {
				type: String,
				default: "children",
			},
			// 是否多选模式
			multiple: Boolean,
			// 单选模式true ,只选子项、false任意一项
			nodes: {
				type: Boolean,
				default: true,
			},
			// 是否选中的数据包含路径
			hasPath: Boolean,
			// 是否关联下级
			checkStrictly: Boolean,
			// 回显的数据
			feedBackList: {
				type: Array,
				default: () => [],
			},
			//每项高度
			itemSize: {
				type: Number,
				default: 40
			}
		},
		computed: {
			// 列表高度
			listHeight() {
				// return this.tree.length * this.itemSize
				return this.positions[this.positions.length - 1].bottom
			},
			// 可显示的列表项数
			visibleCount() {
				return Math.ceil(this.screenHeight / this.itemSize)
			},
			// 偏移量对应的style
			getTransform() {
				return `translate3d(0, ${this.startOffset}px, 0)`
			},
			//获取真实显示列表数据
			visibleData() {
				// return this.tree.slice(this.start, Math.min(this.end, this.tree.length) + 1);
				return this.tree.slice(this.start - this.aboveCount, this.end + this.belowCount);
			},
			isRadioSelect() {
				return (item) => {
					let list = this.selectData;
					return (
						list && list.length > 0 && list[0][this.keyCode] == item[this.keyCode]
					);
				};
			},
			isMulSelect() {
				return (item) => {
					let list = this.selectData;
					// checked 全选 1  halfChecked 半选择 2  不选中 3
					return item.checked ? 1 : item.halfChecked ? 2 : 3;
				};
			},
			// 是否激活
			isActive() {
				return (index) => {
					return index < this.topics.length - 1;
				};
			},
			// 缓冲区的数量
			bufferCount() {
				return this.bufferPercent * this.visibleCount >> 0
			},
			// 上面的缓冲区
			aboveCount() {
				return Math.min(this.start, this.bufferCount)
			},
			// 下面的缓冲区
			belowCount() {
				return Math.min(this.tree.length - this.end, this.bufferCount)
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
		created() {
			
			
		

		},

		mounted() {
			// 空数组表示初始化 init()
			if (!this.feedBackList.length) {
				this.init();
			} else {
				let index = this.feedBackList.length - 1;
				// 存在上个页面选中的数据进行数据回显
				this.selectData = JSON.parse(JSON.stringify(this.feedBackList));
				this.reflexData(this.feedBackList[index][this.keyCode]);
			}
			this.positions = this.initPotions(this.tree)
			// const flat = this.toFlatArray(this.treeNode)
			// console.log('扁平化'，flat);
			this.calcNode()
		},
		updated() {
			// #ifndef MP-WEIXIN
			this.$nextTick(() => {
				// 根据真实元素大小，更新对应的缓存列表
				this.updatePositions(this.positions)
				// 更新完缓存列表后，重新赋值偏移量
				this.getCurrentOffset()
			})
			// #endif

			// #ifdef MP-WEIXIN
			wx.nextTick(() => {
				// 根据真实元素大小，更新对应的缓存列表
				this.updatePositions(this.positions)
				// 更新完缓存列表后，重新赋值偏移量
				this.getCurrentOffset()
			})
			// #endif
		},
		data() {
			return {
				tree: [],
				// 当前选中的数据
				selectData: [],
				topics: [],
				searchResult: [],
				searchRef: null,
				infinityHeight: 'calc(100vh -  140px)',
				//可视区域高度
				screenHeight: 0,
				//偏移量
				startOffset: 0,
				//起始索引
				start: 0,
				//结束索引
				end: null,
				// 预估高度
				preItemSize: 44,
				// 缓存列表
				positions: [
					// 列表项对象
					{
						index: 0, //下标
						top: 0, // 列表项顶部位置 
						bottom: 50, //列表项底部位置
						height: 50 //列表项高度
					}
				],
				// 每个缓存区只缓冲0.5  * 最大可见列表项的个数
				bufferPercent: 0.5
			};
		},
		methods: {
			updatePositions(positions) {
				const query = uni.createSelectorQuery().in(this)
				query.selectAll('.node-list-item').boundingClientRect()
				query.exec(function (res) {
				      //res就是 所有标签为v1的元素的信息 的数组
					 for (let i = 0; i < res[0].length; i++) {
					 	let node = res[0][i]
					 	let {
					 		height
					 	} = node
					 	let { index } = node.dataset
					 	let oldHeight = positions[index]
					 	let dValue = oldHeight - height
					 	if (dValue) {
					 		// 更新对应的列表项
					 		positions[index].height = height
					 		positions[index].bottom = positions[index].bottom - dValue
					 		// 依次更新之后的列表项
					 		for (let k = i + 1; k < positions.length; k++) {
					 			let pot = positions[k]
					 			pot.top = positions[k - 1].bottom
					 			pot.bottom = pot.bottom - dValue
					 		}
					 	}
					 }
				})
				
				// let nodeChildrenList = this.$refs.nodeListRef.$el.children
				// for (let i = 0; i < nodeChildrenList.length; i++) {
				// 	let node = nodeChildrenList[i]
				// 	let {
				// 		height
				// 	} = node.getBoundingClientRect()
				// 	let index = +node.getAttribute('data-index')
				// 	let oldHeight = this.positions[index]
				// 	let dValue = oldHeight - height
				// 	if (dValue) {
				// 		// 更新对应的列表项
				// 		this.positions[index].height = height
				// 		this.positions[index].bottom = this.positions[index].bottom - dValue
				// 		// 依次更新之后的列表项
				// 		for (let k = i + 1; k < this.positions.length; k++) {
				// 			let pot = this.positions[k]
				// 			pot.top = this.positions[k - 1].bottom
				// 			pot.bottom = pot.bottom - dValue
				// 		}
				// 	}
				// }
			},
			initPotions(listData) {
				let potions = listData.map((item, index) => {
					return {
						index,
						top: index * this.itemSize,
						bottom: (index + 1) * this.itemSize,
						height: this.itemSize
					}
				})
				this.start = 0
				return potions
			},
			calcNode() {
				this.query = uni.createSelectorQuery().in(this);
				// #ifndef MP-WEIXIN
				// this.query.select('.search-tree').boundingClientRect(data => {
				// 	console.log('data', data);
				// 	const height = this.infinityHeight + data.height
				// 	this.infinityHeight = `calc(100vh - ${height + "px"})`
				// 	console.log('infinityHeight', this.infinityHeight);
				// }).exec()

				this.$nextTick(() => {
					this.query.select('.infinite-list-container').boundingClientRect(data => {
						console.log('Height', data.height);
						this.screenHeight = data.height
						this.start = 0;
						this.end = this.start + this.visibleCount;
					}).exec()
				})
				// #endif

				// #ifdef MP-WEIXIN
				wx.nextTick(() => {
					this.query.select('.infinite-list-container').boundingClientRect(data => {
						console.log('Height', data.height);
						this.screenHeight = data.height
						this.start = 0;
						this.end = this.start + this.visibleCount;
					}).exec()
				})
				// #endif
			},
			scrollEvent(e) {
				//当前滚动位置
				let scrollTop = e.detail.scrollTop;
				//此时的开始索引
				this.start = this.getStartIndex(scrollTop)
				//此时的结束索引
				this.end = this.start + this.visibleCount;
				//此时的偏移量
				this.getCurrentOffset()
			},
			getStartIndex(scollTop = 0) {
				// let _ = this.positions.find(_ => _ && _.bottom > scollTop)
				// return _.index

				// 二分查找
				return this.binarySearch(this.positions, scollTop)
			},
			binarySearch(positions, target) {
				let left = 0,
					right = positions.length - 1,
					targetIndex = null
				while (left <= right) {
					let midIndex = (left + right) >> 1
					let midVal = positions[midIndex].bottom
					if (midVal == target) {
						return midIndex
					} else if (midVal < target) {
						left = midIndex + 1
						targetIndex = left
					} else {
						right = midIndex - 1
						targetIndex = right
					}
				}
				return targetIndex
			},
			getCurrentOffset() {
				if (this.start >= 1) {
					let size = this.positions[this.start].top - (
						this.positions[this.start - this.aboveCount] ?
						this.positions[this.start - this.aboveCount].top : 0);
					this.startOffset = this.positions[this.start - 1].bottom - size
				} else {
					this.startOffset = 0;
				}
			},
			//搜索
			confirmSearch(val) {
				this.searchResult = []
				this.search(this.treeNode, val)
				const topics = this.topics.slice(0, 1)
				topics.push({
					[this.label]: "搜索结果",
					children: this.searchResult
				})
				uni.showLoading({
					title: '正在查找'
				})
				this.topics = topics
				setTimeout(() => {
					this.tree = this.searchResult
					uni.hideLoading()
					this.$emit('confirmSearch', [...this.searchResult])
				}, 300)
			},
			search(data, keyword) {
				let {
					label,
					children
				} = this
				for (var i = 0, len = data.length; i < len; i++) {
					if (data[i][label].indexOf(keyword) >= 0) {
						this.searchResult.push(data[i])
					}
					if (data[i][children] && data[i][children].length) {
						this.search(data[i][children], keyword)
					}
				}
			},
			// 打开底部弹框
			selectOpen() {
				this.$refs.treePopup.open();
			},
			// 扁平化树形结构
			toFlatArray(tree, parentId = "") {
				return tree.reduce((t, _) => {
					const child = _[this.children];
					return [
						...t,
						parentId ? {
							..._,
							parentId
						} : _,
						...(child && child.length ?
							this.toFlatArray(child, _[this.keyCode]) : []),
					];
				}, []);
			},
			// 回显数据
			reflexData(key) {
				// 根据keyCode找到回显的数据
				// 初始化数据
				const {
					label,
					multiple,
					treeNode,
					dfs,
					handleRadioSelect,
					children,
					checkStrictly,
					handleTreeChecked,
				} = this;
				let _topics = [{
					[label]: "全部",
					children: treeNode
				}];
				let map = [];
				// 通过递归算法找到子节点
				dfs(treeNode, key, map);
				_topics.push(...map);
				// 扁平化树形结构
				// const flats = this.toFlatArray(treeNode, '0')
				// 获取所有关联父节点集合
				// const relativeNode = this.getRelativeNode(flats,key)
				// _topics.push(...relativeNode)
				this.topics = _topics;
				this.tree = Object.freeze(_topics[_topics.length - 1][this.children]);
				checkStrictly && handleTreeChecked();
			},
			// 递归遍历找到回显数据 sonNode子节点, key 回显数据的唯一标识，map找到的子节点所有关联父节点集合
			dfs(sonNode, key, map) {
				for (var i = 0; i < sonNode.length; i++) {
					const chidNode = sonNode[i];
					if (key == chidNode[this.keyCode]) {
						// map.unshift(chidNode)
						return true;
					}
					if (chidNode[this.children] && chidNode[this.children].length) {
						const flag = this.dfs(chidNode[this.children], key, map);
						// 本次的父节点添加到map中
						if (flag) {
							map.unshift(chidNode);
							return true;
						}
					}
				}
			},
			getRelativeNode(flatArray, key) {
				let map = [];
				let child = flatArray.find((_) => _[this.keyCode] == key);
				while (child && child.parentId) {
					map.unshift(child);
					child = flatArray.find((_) => _[this.keyCode] == child.parentId);
				}
				map.length && map.pop();
				return map;
			},
			// 初始化数据
			init() {
				this.topics = [{
					[this.label]: "全部",
					children: this.treeNode
				}];
				this.tree = Object.freeze(this.treeNode);
			},
			// 完成
			handleConfirm() {
				this.$emit("handleConfirm", this.selectData);
				uni.navigateBack();
			},
			handlePath() {
				if (this.hasPath) return [];
				const map = this.topics.map((_) => {
					const topic = Object.assign({}, _)
					delete topic[this.children];
					return topic;
				});
				return map.slice(1, map.length) || [];
			},
			// 点击tab
			handleTopic(index, topic) {
				if (index == 0) {
					this.$refs.searchRef.clear()
				}
				if (!this.isActive(index)) return;
				// 同时删除index后的数据
				this.topics.splice(index + 1, this.topics.length);
				this.tree = Object.freeze(topic[this.children]);
				this.checkStrictly && this.handleTreeChecked();
				this.positions = this.initPotions(this.tree)
			},
			// 点击单选按钮
			handleRadioSelect(item) {
				// 处理路径
				const path = this.handlePath();
				item.path = path;
				this.selectData = [item];
			},
			// 点击多选按钮
			handleMulSelect(item) {
				// 选中状态有全选和半选择
				const {
					checkStrictly,
					keyCode,
					handleSelectRelativeNode,
					handleCancelRelativeNode,
					children,
					handlePath,
				} = this;
				const index = this.selectData.findIndex(
					(_) => _[keyCode] == item[keyCode]
				);
				// 得到路径
				const path = handlePath(item[keyCode])
				// 不关联子级
				if (!checkStrictly) {
					item.checked ?
						this.selectData.splice(index, 1) :
						this.selectData.push(item);
					item.checked = !item.checked;
					item.halfChecked = false;
					// 保存路径
					item.path = path;
					return;
				}
				// 关联子级/取消关联子级
				item.checked || item.halfChecked ?
					handleCancelRelativeNode(item) :
					(this.selectData = this.selectData.concat(
						handleSelectRelativeNode(item, path)
					));
				// 全选状态
				item.checked = item.checked || item.halfChecked ? false : true;
				item.halfChecked = false;
				// 添加路径
				item.path = path;
			},
			// 关联子级
			handleSelectRelativeNode(nodes, path) {
				// 已选中的状态不需要再加进来了 !nodes.checked && !nodes.halfChecked
				let map = [];
				if (!nodes.checked && !nodes.halfChecked) {
					map.push(nodes);
				}
				if (nodes[this.children] && nodes[this.children].length) {
					for (var i = 0; i < nodes[this.children].length; i++) {
						const node = nodes[this.children][i];
						const pathNode = {
							...nodes
						};
						// 删除pathNode中的children属性
						delete pathNode[this.children];
						const _newNode = [...path, pathNode];
						// 添加路径
						node.path = _newNode;
						if (node[this.children] && node[this.children].length) {
							map = map.concat(this.handleSelectRelativeNode(node, _newNode));
						} else {
							if (!nodes.checked && !nodes.halfChecked) {
								map.push(node);
							}
						}
						// 设置全选状态
						node.checked = true;
						node.halfChecked = false;
					}
				}
				return map;
			},
			// 取消关联子级
			handleCancelRelativeNode(nodes) {
				// 删除selectData中的nodes数据
				if (nodes[this.children] && nodes[this.children].length) {
					for (var i = 0; i < nodes[this.children].length; i++) {
						const node = nodes[this.children][i];
						this.handleCancelRelativeNode(node);
						node.checked = false;
						node.halfChecked = false;
					}
				}
				const index = this.selectData.findIndex(
					(_) => _[this.keyCode] == nodes[this.keyCode]
				);
				index != -1 && this.selectData.splice(index, 1);
			},
			// 下一级
			handleTreeNode(node, index) {
				// 没有子项不点击
				if (!node[this.children] || !node[this.children].length) return;
				// 处理上面的tab
				this.handleTopics(node);
				this.tree = node[this.children];
				this.positions = this.initPotions(this.tree)
				// 关联子级
				this.checkStrictly && this.handleTreeChecked();
			},
			// 处理当前节点和子节点的选中与半选择与不选中的状态
			handleTreeChecked() {
				for (var i = 0; i < this.tree.length; i++) {
					const treeItem = this.tree[i];
					// 没有子节点就不需要去改变他的选中状态
					if (!treeItem[this.children] || !treeItem[this.children].length)
						continue;
					// 有子节点去计算出子节点的状态 treeNode子节点的数量  treeNodeSelect选中的子节点的数量
					const {
						treeNode = 0, treeNodeSelect = 0
					} = this.handleCalcNodeAccount(
						treeItem[this.children],
						0,
						0
					);
					// 初始状态0 =0不算  子节点的数量 == 选中的子节点的数量 => 全选状态
					if (treeNode != 0 && treeNodeSelect != 0) {
						// 全选
						if (treeNode == treeNodeSelect) {
							treeItem.checked = true;
							treeItem.halfChecked = false;
						}
						//半选
						else {
							treeItem.checked = false;
							treeItem.halfChecked = true;
						}
					} else {
						treeItem.checked = false;
						treeItem.halfChecked = false;
					}
				}
			},
			handleCalcNodeAccount(children, treeNode, treeNodeSelect) {
				for (var i = 0; i < children.length; i++) {
					const child = children[i];
					if (child[this.children] && child[this.children].length) {
						const treeAccount = this.handleCalcNodeAccount(
							child[this.children],
							treeNode,
							treeNodeSelect
						);
						treeNode = treeAccount.treeNode;
						treeNodeSelect = treeAccount.treeNodeSelect;
					}
					treeNode++;
					// 判断节点是否存在于selectData已选择的数据中，如果存在表示选中，不存在表示不选中
					const findIndex = this.selectData.findIndex(
						(_) => _[this.keyCode] == child[this.keyCode]
					);
					findIndex != -1 && treeNodeSelect++;
				}
				return {
					treeNode,
					treeNodeSelect,
				};
			},
			// 处理上面的tab
			handleTopics(node) {
				// tabs 列表下标赋值
				// 添加到tab列表中
				this.topics.push(node);
			},
		},
	};
</script>

<style scoped lang="scss">
	@import "./css/style.scss";
	@import url("./css/icon.css");
</style>