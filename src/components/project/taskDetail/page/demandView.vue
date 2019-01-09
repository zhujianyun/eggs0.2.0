<template>
    <div class="demand_view">
        <div class="top_box">
            <div class="left fl">
            <el-checkbox 
                v-if='checkedList.length' 
                class="all_checked" 
                v-model="fileCheckbox" 
                @change='fileCheckboxAll'
                >
                已选<span class="mainColor">{{checkedList.length}}</span>项
            </el-checkbox>
            <el-checkbox 
                v-else 
                class="all_checked" 
                v-model="fileCheckbox" 
                @change='fileCheckboxAll'>全选</el-checkbox>
                <template v-if='checkedList.length'>
                <i class="iconfont icon-xiazai"></i>
                <i class="iconfont icon-shoucang1"></i>
                <i class="iconfont icon-jihuayijiao"></i>
                </template>
            </div>
        </div>
        <div class="demand_box">

        </div>
    </div>
</template>
<script>
export default {
    props: ['list'],
    data() {
        return {
            checkedList: [],
            fileCheckbox: false,
            demandList: []

        }
    },
    methods: {
        fileCheckboxAll() {

        },
        setData() {
            this.checkedList = [...this.list];
            console.log('相关需求~~~~', this.checkedList);
        },
    },
    created() {
        this.setData();
    }
}
</script>
<style lang='less'>
    .demand_view {
        width: 100%;
        height: 100%;
        background: #ffffff;
        .demand_box {

        }
    }
</style>


