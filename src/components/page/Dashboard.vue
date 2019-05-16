<template>
    <div>
        <el-row :gutter="24" class="mgb20">
            <el-col :span="8">
                <el-card shadow="hover" class="mgb20">
                    <div class="user-info">
                        <img :src="user.userAvatar" class="user-avator">
                        <div class="user-info-cont">
                            <div class="user-name">{{name}}</div>
                            <div>{{role}}</div>
                        </div>
                    </div>
                    <span>所在地：</span><span>{{location.basic.cnty}}  {{location.basic.admin_area}}  {{location.basic.location}}&nbsp;&nbsp;&nbsp;&nbsp;{{location.now.cond_txt}}</span>
                </el-card>
            </el-col>
            <el-row :span="18">
                <el-col :span="5">
                    <el-card shadow="hover" :body-style="{padding: '15px'}">
                        <div class="grid-content grid-con-1">
                            <i class="el-icon-view grid-con-icon"></i>
                            <div class="grid-cont-right">
                                <div class="grid-num">{{homeCount.userCount}}</div>
                                <div>注册用户总数</div>
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="5">
                    <el-card shadow="hover" :body-style="{padding: '15px'}">
                        <div class="grid-content grid-con-2">
                            <i class="el-icon-message grid-con-icon"></i>
                            <div class="grid-cont-right">
                                <div class="grid-num">{{homeCount.pinsCount}}</div>
                                <div>发布沸点总数</div>
                            </div>
                        </div>
                    </el-card>
                </el-col>
                <el-col :span="5">
                    <el-card shadow="hover" :body-style="{padding: '15px'}">
                        <div class="grid-content grid-con-3">
                            <i class="el-icon-tickets grid-con-icon"></i>
                            <div class="grid-cont-right">
                                <div class="grid-num">{{homeCount.articlesCount}}</div>
                                <div>发布文章总数</div>
                            </div>
                        </div>
                    </el-card>
                </el-col>
            </el-row>
        </el-row>
        <el-row :gutter="20">
            <el-col :span="12">
                <el-card shadow="hover">
                <div slot="header" class="clearfix">
                <span>标签中文章占比</span>
                </div>
                    <schart :canvasId="canvasId1" :type="type1" :data="data1"></schart>
                </el-card>
            </el-col>
            <el-col :span="12">
                <el-card shadow="hover">
                    <div slot="header" class="clearfix">
                        <span>话题中沸点占比</span>
                    </div>
                    <schart :canvasId="canvasId2" :type="type2" :data="data2"></schart>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import Schart from 'vue-schart';
    export default {
        data() {
            return {
                user:JSON.parse(localStorage.getItem('user')),
                name: localStorage.getItem('ms_username'),
                location:'',
                homeCount:{},
                canvasId1: 'myCanvas1',
                type1: 'pie',
                data1: [{name:'',value:''}],
                canvasId2: 'myCanvas2',
                type2: 'bar',
                data2: [{name:'',value:''}],
            }
        },
        components:{
            Schart
        },
        created(){
            this.getTopicData();
            this.getLabelData();
            this.getCount();
            this.getLocation();
        },
        methods:{
            getLocation(){
                var that=this;
                this.$axios
                    .get("https://free-api.heweather.com/s6/weather?location=auto_ip&key=a46fd5c4f1b54fda9ee71ba6711f09cd")
                    .then(function (res) {
                        that.location=res.data.HeWeather6[0];
                        console.log(res.data.HeWeather6[0])
                    });
            },
            getCount(){
                var that=this;
                this.$axios
                    .get(this.$baseUrl+"/manage/homeCount")
                    .then(function (res) {
                        that.homeCount=res.data.data;
                    })
            },
            getTopicData(){
                var that=this;
                this.$axios
                    .get(this.$baseUrl+"/manage/topicPin")
                    .then(function (res) {
                        that.data2=res.data.data;
                    })
            },
            getLabelData(){
                var that=this;
                this.$axios
                    .get(this.$baseUrl+"/manage/labelArticle")
                    .then(function (res) {
                        that.data1=res.data.data;
                    })
            }
        },
        computed: {
            role() {
                return this.user.email === 'admin' ? '超级管理员' : '普通用户';
            }
        }
    }

</script>


<style scoped>
    .grid-content {
        display: flex;
        align-items: center;
        height: 100px;
    }

    .grid-cont-right {
        flex: 1;
        text-align: center;
        font-size: 12px;
        color: #999;
    }

    .grid-num {
        font-size: 30px;
        font-weight: bold;
    }

    .grid-con-icon {
        font-size: 50px;
        width: 100px;
        height: 100px;
        text-align: center;
        line-height: 100px;
        color: #fff;
    }

    .grid-con-1 .grid-con-icon {
        background: rgb(45, 140, 240);
    }

    .grid-con-1 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-2 .grid-con-icon {
        background: rgb(100, 213, 114);
    }

    .grid-con-2 .grid-num {
        color: rgb(100, 213, 114);
    }

    .grid-con-3 .grid-con-icon {
        background: rgb(242, 94, 67);
    }

    .grid-con-3 .grid-num {
        color: rgb(242, 94, 67);
    }

    .user-info {
        display: flex;
        align-items: center;
        padding-bottom: 20px;
        border-bottom: 2px solid #ccc;
        margin-bottom: 20px;
    }
    .user-name{
        font-size: 10px;
    }
    .user-avator {
        width: 120px;
        height: 120px;
        border-radius: 50%;
    }

    .user-info-cont {
        padding-left: 50px;
        flex: 1;
        font-size: 14px;
        color: #999;
    }

    .user-info-cont div:first-child {
        font-size: 30px;
        color: #222;
    }
    .user-info-list span {
        margin-left: 70px;
    }

    .mgb20 {
        margin-bottom: 20px;
    }

    .todo-item {
        font-size: 14px;
    }

</style>
