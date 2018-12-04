<template>
    <div>
        <h2>日期选择器</h2>
        <div id="center">
            <div class="moren">
                <span class="demonstration">默认</span>
                <el-date-picker
                    v-model="value1"
                    type="date"
                    placeholder="选择日期">
                </el-date-picker>
            </div>
            <div class="kuaijie">
                <span class="demonstration">带快捷选项</span>
                <el-date-picker
                    v-model="value2"
                    align="right"
                    type="date"
                    placeholder="选择日期"
                    :picker-options="pickerOptions1">
                </el-date-picker>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() > Date.now();
                    },
                    shortcuts: [{
                        text: '今天',
                        onClick(picker) {
                            picker.$emit('pick', new Date());
                        }
                    }, {
                        text: '昨天',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24);
                            picker.$emit('pick', date);
                        }
                    }, {
                        text: '一周前',
                        onClick(picker) {
                            const date = new Date();
                            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', date);
                        }
                    }]
                },
                value1: '',
                value2: '',
            };
        }
    };
</script>

<style scoped>
    .moren{
        width: 300px;
        float: left;
        margin-left: 40px;
    }
    .kuaijie{
        width: 350px;
        float: left;
    }
    #center{
        background-color:#5FB878;
        width: 700px;
        height: 150px;
        margin-left: 120px;
        margin-top: 100px;
        line-height: 150px;
        border-radius: 30px;
    }
</style>
