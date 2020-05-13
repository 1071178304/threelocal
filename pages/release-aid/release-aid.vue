<template>
    <view>
        <view>
            <form @submit="formSubmit">
                <view class="relase-input">
                    <text class="realse-name">所在地</text>
                    <addressd @changes="childClick"></addressd>
                </view>
            </form>
        </view>
	</view>
</template>

<script>
import addressd from "@/components/jm-address/jm-address2.vue"
	export default {
        data() {
            return {
                localcity: ''
            }
        },
        components: { uploadImg },
        methods: {
            // 三级联动
            childClick(e) {
                console.log(e)
                this.localcity = e.procity+e.province+e.city
                console.log("input",this.localcity)
            },
            // end
            formSubmit(localcity) {
                var that = this;
                console.log("三级菜单",that.localcity);
                uni.request({
                    url: '',
                    method: 'POST',
                    data: {
                        region: that.localcity
                    },
                    success: (res) => {
                        uni.showToast({
                        	title: "提交成功"
                        })
                        console.log("表单",res);
                    }
                })
            },
        },
	}
</script>

<style lang="scss" scoped>
.bor {border: 1rpx solid;}
// input
.relase-input {
    background: white;
    height: 100rpx;
    line-height: 100rpx;
    font-size: 28rpx;
    color: #303133;
    .realse-name {
        float: left;
        width: 22%;
        padding-left: 39rpx;
    }
}
</style>
