<!-- 本插件只是个框架,适用于地址数据要从后台逐级获取的需求 -->
<!-- 可对照注释根据业务需求更改 -->
<!-- 作者：qq315500033 -->

<template>
	<view>
		<!-- 选择地址展示 -->
		<view @tap="showAddress">
			{{coname}} - {{ctname}} - {{csname}}
		</view>
		<!-- 选择地址模态框 -->
		<view class="jm-modal" :class="showFlag==true?'show1':''">
			<view class="dialog">
				<view class="showBox">
					<view class="content">选择地址</view>
					<!-- 关闭按钮 -->
					<view class="action" @tap="hideAddress">
						x
					</view>
				</view>
				<view class="choice">
					已选： {{coname}} - {{ctname}} - {{csname}}
				</view>
				<!-- 省份列表 -->
				<view class="addList">
					<view v-for="(item,index) in addressd1" :key='index' :class="{check:item.id==co}" @tap="clickAddress(1,item.id,item.name)">
						{{item.name}}
					</view>
				</view>
				<!-- 城市列表 -->
				<view class="addList">
					<view v-for="(item,index) in addressd2" :key='index' :class="{check:item.id==ct}" @tap="clickAddress(2,item.id,item.name)">
						{{item.name}}
					</view>
				</view>
				<!-- 地区列表 -->
				<view class="addList">
					<view v-for="(item,index) in addressd3" :key='index' :class="{check:item.id==cs}" @tap="clickAddress(3,item.id,item.name)">
						{{item.name}}
					</view>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
	var _self;
	export default {
		data() {
			return {
				//模态框状态
				showFlag: false,
				// 省份列表
				addressd1: [],
				// 城市列表
				addressd2: [],
				// 地区列表
				addressd3: [],
				//省份id 默认为64，可根据想要默认展示的id自行更改
				co: '1',
				//默认省份名称
				coname: '北京',
				//城市id 默认为64，可根据想要默认展示的id自行更改
				ct: '64',
				//默认城市名称
				ctname: '朝阳区',
				//地区id 默认为575，可根据想要默认展示的id自行更改
				cs: '575',
				//默认地区名称
				csname: '三环以内'
			};
		},
		props: {
			//载入的标签数据
			addressd: Array
		},
		mounted() {
			_self = this;
			//默认获取省份列表
			this.getadd(1, 0);
			//默认获取城市列表
			this.getadd(2, this.co);
			//默认获取地区列表
			this.getadd(3, this.ct);
			// 声明默认地址,并传送给父组件
			this.emitData();

		},
		methods: {
			//呼出模态框
			showAddress() {
				// 呼出模态框
				this.showFlag = true
			},
			// 关闭模态框
			hideAddress() {
				// 关闭模态框
				this.showFlag = null;
				// 声明默认地址,并传送给父组件
				this.emitData();

			},
			// 声明默认地址,并传送给父组件
			emitData() {
				var data = {
					province: _self.coname,
					city: _self.ctname,
					district: _self.csname
				}
				_self.$emit("changes", data);
			},
			// 模态框地址点击赋值并获取下一级
			// --flag--- 1(省份点击);2(城市点击);3(地区点击)；
			// --id----(点击的地址id);
			// --name--(点击的地址名称);
			clickAddress(flag, id, name) {
				//判断点击的状态
				switch (flag) {
					case 1:
						_self.coname = name;
						_self.co = id;
						_self.getadd(2, id);
						break;
					case 2:
						_self.ctname = name;
						_self.ct = id;
						_self.getadd(3, id);
						break;
					case 3:
						_self.csname = name;
						_self.cs = id;
						_self.emitData();
						_self.hideAddress();
						break;
					default:
						return;
				}

			},
			// ======================demo===============================================
			// 
			// 			getadd(flag, id) {
			// 				uni.request({
			// 					url: 'xxxx', //仅为示例，并非真实接口地址。
			// 					data: {
			// 						pid: id
			// 					},
			// 					success: function(res) {
			// 						switch (flag) {
			// 							case 1:
			// 								_self.addressd1 = res.data;
			// 								break;
			// 							case 2:
			// 								_self.addressd2 = res.data;
			// 								_self.ctname = res.data[0].name;
			// 								_self.ct = res.data[0].id;
			// 								_self.getadd(3, _self.ct);
			// 								break;
			// 							case 3:
			// 								_self.addressd3 = res.data;
			// 								_self.csname = res.data[0].name;
			// 								_self.cs = res.data[0].id;
			// 								break;
			// 							default:
			// 								return;
			// 						}
			// 					}
			// 				});

			// ==============================================================================


			// 模拟获取数据,真实场景可参考上面demo
			getadd(flag, id) {
				switch (flag) {
					case 1:
						_self.addressd1 = addObj;
						break;
					case 2:
						var newArr = [];
						for (var i = 0; i < addObj.length; i++) {
							if (addObj[i].id == id) {
								newArr = addObj[i].city
							}
						}
						_self.addressd2 = newArr;
						_self.ctname = newArr[0].name;
						_self.ct = newArr[0].id;
						_self.getadd(3, _self.ct);
						break;
					case 3:
						// 根据业务做相应处理
						break;
					default:
						return;
				}
			}
		}
	}
	var addObj = [{
			"id": 1,
			"code": "",
			"name": "北京市",
			// "parent_id": 0,
			"level": 1,
			"path": "0,",
			city: [{
				"id": 575,
				"code": "",
				"name": "三环以内",
				"parent_id": 64,
				"level": 3,
				"path": "0,1,64,"
                
			}, {
				"id": 576,
				"code": "",
				"name": "三环到四环之间",
				"parent_id": 64,
				"level": 3,
				"path": "0,1,64,"
			}, {
				"id": 577,
				"code": "",
				"name": "四环到五环之间",
				"parent_id": 64,
				"level": 3,
				"path": "0,1,64,"
			}, {
				"id": 578,
				"code": "",
				"name": "五环到六环之间",
				"parent_id": 64,
				"level": 3,
				"path": "0,1,64,"
			}, {
				"id": 579,
				"code": "",
				"name": "管庄",
				"parent_id": 64,
				"level": 3,
				"path": "0,1,64,"
			}]
		}, {
			"id": 2,
			"code": "",
			"name": "上海",
			"parent_id": 0,
			"level": 1,
			"path": "0,",
			city: [{
				"id": 82,
				"code": "",
				"name": "黄浦区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 83,
				"code": "",
				"name": "徐汇区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 84,
				"code": "",
				"name": "长宁区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 85,
				"code": "",
				"name": "静安区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 86,
				"code": "",
				"name": "闸北区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 87,
				"code": "",
				"name": "虹口区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 88,
				"code": "",
				"name": "杨浦区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}, {
				"id": 89,
				"code": "",
				"name": "宝山区",
				"parent_id": 2,
				"level": 2,
				"path": "0,2,"
			}]
		}, {
			"id": 3,
			"code": "",
			"name": "天津",
			"parent_id": 0,
			"level": 1,
			"path": "0,",
			city: [{
				"id": 99,
				"code": "",
				"name": "东丽区",
				"parent_id": 3,
				"level": 2,
				"path": "0,3,"
			}, {
				"id": 100,
				"code": "",
				"name": "和平区",
				"parent_id": 3,
				"level": 2,
				"path": "0,3,"
			}, {
				"id": 101,
				"code": "",
				"name": "河北区",
				"parent_id": 3,
				"level": 2,
				"path": "0,3,"
			}, {
				"id": 102,
				"code": "",
				"name": "河东区",
				"parent_id": 3,
				"level": 2,
				"path": "0,3,"
			}, {
				"id": 103,
				"code": "",
				"name": "河西区",
				"parent_id": 3,
				"level": 2,
				"path": "0,3,"
			}]
		},



	];
</script>

<style>
	.choice {
		background-color: #fff;
		padding: 20upx;
		font-size: 28upx;
	}

	.addList {
		height: 570upx;
		box-sizing: border-box;
		overflow-y: scroll;
		width: 31%;
		float: left;
		margin-left: 1%;
		margin-right: 1%;
		padding: 200upx 0;
		font-size: 28upx;
	}

	.addList view {
		height: 70upx;
		line-height: 70upx;
	}

	.check {
		color: #fff;
		background-color: #bd1e2c;
	}

	.jm-modal {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: 1110;
		opacity: 0;
		outline: 0;
		text-align: center;
		-ms-transform: scale(1.185);
		transform: scale(1.185);
		backface-visibility: hidden;
		perspective: 2000upx;
		background: rgba(0, 0, 0, 0.6);
		transition: all 0.3s ease-in-out 0s;
		pointer-events: none;
	}

	.jm-modal::before {
		content: "\200B";
		display: inline-block;
		height: 100%;
		vertical-align: middle;
	}

	.show1 {
		opacity: 1;
		transition-duration: 0.3s;
		-ms-transform: scale(1);
		transform: scale(1);
		overflow-x: hidden;
		overflow-y: auto;
		pointer-events: auto;
	}

	.dialog {
		position: relative;
		display: inline-block;
		vertical-align: middle;
		margin-left: auto;
		margin-right: auto;
		width: 680upx;
		max-width: 100%;
		height: 700upx;
		background-color: #f8f8f8;
		border-radius: 10upx;
		overflow: hidden;
	}

	.action {
		position: absolute;
		right: 30upx;
	}

	.content {
		position: absolute;
		text-align: center;
		width: calc(100% - 340upx);
		left: 0;
		right: 0;
		bottom: 0;
		top: 0;
		margin: auto;
		height: 60upx;
		font-size: 32upx;
		line-height: 60upx;
		cursor: none;
		pointer-events: none;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
	}

	.showBox {
		background-color: #fff;
		display: flex;
		position: relative;
		align-items: center;
		min-height: 100upx;
		justify-content: space-between;
	}
</style>
