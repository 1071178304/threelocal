<template>
    <view>
        <div >
            <addressd @changes="childClick"></addressd>
        </div>
    </view>
</template>

<script>
    import addressd from "@/components/jm-address/jm-address.vue"
    export default {
        data() {
            return {
                title: 'Hello'
            }
        },
        components: { addressd },
        onLoad() {

        },
        methods: {
            childClick(e) {
                console.log(e)
            }
        }
    }
</script>

<style>

</style>
