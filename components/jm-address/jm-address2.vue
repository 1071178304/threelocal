<!-- uniapp三级联动，根据需求修改 该插件参考着为qq315500033 -->
<!-- 作者yyx 二级联动 -->
<template>
    <view>
        <!-- 选择地址展示 -->
        <view @tap="showAddress">
            {{procity}} - {{coname}} - {{ctname}}
            <image class="three-img" src="../../static/index/right.png"></image>
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
                    已选： {{procity}} - {{coname}} - {{ctname}}
                </view>
                <!-- 省份列表 -->
                <view class="addList">
                    <view>
                        {{procity}}
                    </view>
                </view>
                <view class="addList">
                    <view v-for="(item,index) in addressd1" :key='index' :class="{check:item.id==co}" @tap="clickAddress(1,item.id,item.city)">
                        {{item.city}}
                    </view>
                </view>
                <!-- 城市列表 -->
                <view class="addList">
                    <view v-for="(item,index) in addressd2" :key='index' :class="{check:item.id==ct}" @tap="clickAddress(2,item.id,item.city)">
                        {{item.city}}
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
            coname: '长春市',
            //城市id 默认为64，可根据想要默认展示的id自行更改
            ct: '64',
            //默认城市名称
            ctname: '长春市',
            //地区id 默认为575，可根据想要默认展示的id自行更改
            cs: '575',
            //默认地区名称
            procity: '吉林省',
            // csname: '三环以内'
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
                procity: _self.procity,
                province: _self.coname,
                city: _self.ctname,

                // district: _self.csname
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
                default:
                    return;
            }

        },
        getadd(flag, id) {
            uni.request({
                url: '', //自己的接口
                method: 'GET',
                success: function(res) {
                    if (res.data.code == 1) {
                        console.log(1111111, res.data.data.parte.children[0].children)
                        console.log(111, res.data.data.parte.city)
                        _self.procity = res.data.data.parte.city;
                        switch (flag) {
                            case 1:
                                _self.addressd1 = res.data.data.parte.children;
                                break;
                            case 2:
                                _self.addressd2 = res.data.data.parte.children[0].children;
                                _self.ctname = res.data.data.parte.children[0].children[0].city;
                                _self.ct = res.data.data.parte.children[0].children[0].id;
                                _self.getadd(3, _self.ct);
                                break;
                            default:
                                return;
                        }
                    }
                }
            });
        }
    }
}
</script>

<style>
.three-img {
    float: right;
    width: 13rpx;
    height: 24rpx;
    padding-top: 38rpx;
    padding-right: 40rpx;
}

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
