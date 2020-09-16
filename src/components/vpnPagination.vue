<template>
    <el-pagination
            class="myPag"
            background
            :pager-count="10"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="offset"
            :page-sizes="[10, 20, 30, 40]"
            :page-size="pageSize"
            layout=" sizes, prev, pager, next, jumper"
            :total="total">
    </el-pagination>
</template>

<script>
    export default {
        name: "vpnPagination",
        data() {
            return {

            }
        },
        props: {
            total:{      // 总条目数
                type: Number,
                default: 1   //默认值
            },
            pageSize:{      //每页显示条目个数
                type: Number,
                default: 1
            },
            offset:{        //当前页数
                type: Number,
                default: 1
            },
        },
        mounted() {
        },
        methods: {
            handleSizeChange(val) {      //pageSize 改变时会触发，事件放在父组件中处理
                this.$emit('handleSizeChangeSub', val);
            },
            handleCurrentChange(val) {   //currentPage 改变时会触发
                this.$emit('handleCurrentChangeSub', val);
            }
        },
    }
</script>

<style scoped>
    .myPag{
        flex :1;
        float: right;
        margin: 24px 0;
    }

</style>
