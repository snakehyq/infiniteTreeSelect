# hyq-tree-vtw
无限极树形结构面包屑、单选-多选、搜索、移除功能

## 示例项目

![示例项目](https://img-blog.csdnimg.cn/direct/4ba84d793df345fcb5dba7c3d8c11040.png#pic_center)

## 单选-user

![单选只选user](https://img-blog.csdnimg.cn/direct/ffadcf4f93c64a3abc1312808d8fb79d.png#pic_center)

## 单选-任意一项

![单选任意选择](https://img-blog.csdnimg.cn/direct/6f9fdbd2fdd245139919eb7bba17275f.png#pic_center)

## 多选-关联下级

![多选关联下级](https://img-blog.csdnimg.cn/direct/d0aedc93eef147a6930298d3db8e27e4.png#pic_center)

## 多选-任意一项

![多选任选一级](https://img-blog.csdnimg.cn/direct/1647bc2c9000435d86f0186dc777e52f.png#pic_center)

## 已选择数据弹框
![已选数据](https://img-blog.csdnimg.cn/direct/c31b996fec83438abbb75b359b7dd48f.jpeg#pic_center)

## 说明

- 本插件需要使用uni-popup、uni-transition用于已选择数据弹框，因此需要有这些依赖,请自行导入
- 本插件基于【虚拟列表】高性能渲染海量数据，加入动态高度、缓冲区
- 本人只在微信小程序端和H5 使用Chrome浏览器测试和微信开发者工具

## 安装方式

本组件符合[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)规范，HBuilderX 3.1.0起，只需将本组件导入项目，在页面`template`中即可直接使用，无需在页面中`import`和注册`components

## 基本用法

```js
  <hyq-tree-vtw
    label="label"
    children="children"
    key-code="keyCode"
    :tree-node="treeNode"
    :feed-back-list="feedBackList"
    is-check
    show-search
    @handleConfirm="handleConfirm"
  ></hyq-tree-vtw>
```

```js

<script>
	import {
		treeNode
	} from './data.js'
	export default {
		data() {
			return {
				treeNode,
				feedBackList: []
			}
		},
		methods: {
			handleConfirm(val) {
				console.log('val', val)
			}
		}
	}
</script>
```

## dat.js 数据生成

```js
const treeNode = [{
		name: '一级',
		id: '1',
		user: false,
		children: [{
				name: '二级-1',
				id: '2-1',
				user: false,
				children: [{
						name: '三级-1',
						id: '3-1',
						user: false,
						children: [{
								name: '四级-1',
								id: '4-1',
								user: false,
								children: [{
										name: '五级-1',
										id: '5-1',
										user: false,
										children: [{
												name: '六级-1',
												id: '6-1',
												user: true,
												children: [

												]
											},
											...makeTreeNode(5)
										]
									},
									...makeTreeNode(4)
								]
							},
							...makeTreeNode(3)
						]
					},
					...makeTreeNode(2)
				],
			},
			...makeTreeNode(1)
		]
	},
	{
		name: '一级-2',
		id: '1-1-1',
		user: false,
		children: [{
				name: '1-二级-1',
				id: '1-6-1665',
				user: false,
				children: [{
						name: '1-三级-1',
						id: '1-5-1',
						user: false,
						children: [{
								name: '1-四级-1',
								id: '1-6-166',
								user: true,
								children: [
									...makeTreeNode('1-四级-1')
								]
							},
							...makeTreeNode('1-三级-1')
						]
					},
					...makeTreeNode('2-1')
				]
			},
			...makeTreeNode('1-1')
		]
	},
]

function makeTreeNode(leval) {
	let treeNoneList = []
	for (let k = 0; k < 100; k++) {
		let name = `${leval}级-${k}`
		treeNoneList.push({
			name,
			id: guid(),
			user: true
		})
	}
	return treeNoneList
}

function guid() {
	function S4() {
		return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
	}
	return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
}

export {
	treeNode
};

```



## 传入的数据结构说明-treeNode

```js
[
    {
        id: 664214366998,
        name: "校长443",
        children: [{
            id: 885655454545454545678,
            name: "小陆"
        }]
    },
]
```

## 选中返回的结果feedBackList：返回一个二维数组

```js
[
    {
        id: xxx,
        name: "xxx",
        path:[],//该值的路径
    },
]
```



## 功能说明

1、树形结构展示

2、包含搜索框、面包屑导航

3、单选模式（只选user）、单选模式（选择任意一项）、多选模式（关联下级）、多选模式（选择任意一项）

4、只需传checkList字段就可以回显默认选中

5、已选数据可以进行移除

## 属性

| 属性名        | 类型    | 默认值   | 说明                                                         |
| ------------- | ------- | -------- | ------------------------------------------------------------ |
| isCheck       | Boolean | false    | 是否选中                                                     |
| showSearch    | Boolean | false    | 是否开启搜索                                                 |
| keyCode       | String  | id       | 数据唯一标识（id列的属性名）                                 |
| label         | String  | name     | 指定选项标签为选项对象的某个属性值                           |
| children      | String  | children | 指定选项的子选项为选项对象的某个属性值                       |
| treeNode      | Array   | []       | trees 传入的树形结构，每个对象必须包含唯一的id值             |
| multiple      | Boolean | false    | 是否开启多选模式                                             |
| nodes         | Boolean | false    | 是否开启单选模式的只选择子项，true ：只选子项、false：任意一项 |
| hasPath       | Boolean | false    | 是否开启选中的数据包含路径                                   |
| checkStrictly | Boolean | false    | 多选模式下是否要关联下级                                     |
| feedBackList  | Array   | []       | 选中的列表                                                   |

## 事件

| 事件名         | 说明                   | 返回值                 |
| -------------- | ---------------------- | ---------------------- |
| @handleConfirm | 点击完成按钮时触发事件 | 参数（选中的项值）     |
| @confirmSearch | 搜索完成后触发事件     | 参数（搜索结果的项值） |

## 单选模式（只选user）

```js
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
```

## 单选模式（选择任意一项）

```js
<template>
	<view class="content">
		<hyq-tree-vtw :label="cprop.label" :children="cprop.children" :key-code="cprop.keyCode"
			:has-path="cprop.hasPath" :nodes="cprop.nodes" :multiple="cprop.multiple"
			:checkStrictly="cprop.checkStrictly" :tree-node="treeNode" :feed-back-list="feedBackList" is-check
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
				cprop: { //单选模式(任意一项)
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: false,
					nodes: false,
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
```

## 多选模式（关联下级）

```js
<template>
	<view class="content">
		<hyq-tree-vtw :label="bprop.label" :children="bprop.children" :key-code="bprop.keyCode"
			:has-path="bprop.hasPath" :nodes="bprop.nodes" :multiple="bprop.multiple"
			:checkStrictly="bprop.checkStrictly" :tree-node="treeNode" :feed-back-list="feedBackList" is-check
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
				bprop: {
					label: 'name',
					children: 'children',
					keyCode: 'id',
					multiple: true,
					checkStrictly: true,
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
```

## 多选模式（选择任意一项）

```js
<template>
	<view class="content">
		<hyq-tree-vtw :label="aprop.label" :children="aprop.children" :key-code="aprop.keyCode"
			:has-path="aprop.hasPath" :nodes="aprop.nodes" :multiple="aprop.multiple"
			:checkStrictly="aprop.checkStrictly" :tree-node="treeNode" :feed-back-list="feedBackList" is-check
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
```



# github源码地址

[github](https://github.com/snakehyq/infiniteTreeSelect)

## vue3版本

[点击地址](https://ext.dcloud.net.cn/plugin?id=15785)