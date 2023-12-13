# hyq-tree-vtw
无限极树形结构面包屑、单选-多选、搜索、移除功能

## 首页

![image-20230918170058899](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-20230918170058899.png)

## 单选-user

![image-20230918170138002](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-20230918170138002.png)

## 单选默认-任意一项

![image-20230918170206345](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-20230918170206345.png)



## 多选-关联下级

![image-20230918170231896](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-20230918170231896.png)

## 多选-任意一项

![image-20230918170303436](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-20230918170303436.png)

## 已选择数据弹框
![image-20230918170303436](https://github.com/snakehyq/infiniteTreeSelect/blob/main/static/imags/image-1695286886689.jpg)

## 说明

- 本插件需要使用uni-popup、uni-transition用于已选择数据弹框，因此需要有这些依赖
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

| 事件名         | 说明                   | 返回值             |
| -------------- | ---------------------- | ------------------ |
| @handleConfirm | 点击完成按钮时触发事件 | 参数（选中的项值） |

## 单选模式（只选user）

## 单选模式（选择任意一项）

## 多选模式（关联下级）

## 多选模式（选择任意一项）



# github源码地址

[github](https://github.com/snakehyq/infiniteTreeSelect)