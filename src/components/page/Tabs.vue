<template>
    <div>
        <div class="search">
            <el-input
                v-model="search"
                size="large"
                placeholder="输入关键字搜索"/>
        </div>
        <div class="addTopic">
            <el-button
                size="large"
                @click="handleUpload()"
                type="success">新增</el-button>
        </div>
        <el-table :data="tableData.filter(data => !search || data.labels.labelsName.toLowerCase().includes(search.toLowerCase()))"
                  class="tb-edit" style="width: 100%"
                  border highlight-current-row>
            <el-table-column prop="labels.labelsId" label="ID" fixed width="80" align="center" :show-overflow-tooltip="true"></el-table-column>
            <el-table-column label="标签图标" width="150" align="center">
                <template slot-scope="scope">
                    <img v-bind:src="scope.row.labels.labelsUrl" class="pic"/>
                </template>
            </el-table-column>
            <el-table-column label="标签名称" align="center">
                <template slot-scope="scope">
                    <el-input size="small" v-model="scope.row.labels.labelsName"></el-input>
                    <span>{{scope.row.labels.labelsName}}</span>
                </template>
            </el-table-column>
            <el-table-column label="关注人数" prop="concernCount" width="100" align="center" :show-overflow-tooltip="true"></el-table-column>
            <el-table-column label="文章数" prop="articleCount" width="100" align="center" :show-overflow-tooltip="true">
            </el-table-column>
            <el-table-column label="操作" width="150" header-align="center" align="right">
                <template slot-scope="scope">
                    <el-button
                        size="mini"
                        type="success"
                        @click="saveEdit(scope.row.labels)">保存</el-button>
                    <el-button
                        size="mini"
                        type="danger"
                        @click="handleDelete(scope.row.labels)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--新增弹出框-->
        <el-dialog
            title="新增标签"
            :visible.sync="uploadVisible" width="20%"
            :close-on-click-modal="false">
            <div class="el-row">
                <div class="el-col-md-20">
                    <el-form :model="form" label-width="80px">
                        <el-form-item label="标签名称:">
                            <el-input v-model="form.labelName" ></el-input>
                        </el-form-item>
                    </el-form>
                    <el-upload
                        class="avatar-uploader"
                        action="customize"
                        ref="upload"
                        :auto-upload="false"
                        :show-file-list="false"
                        :on-change="changeUpload"
                        :http-request="uploadFile"
                        accept=".jpg">
                        <img v-if="imageUrl" :src="imageUrl" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </div>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="uploadVisible = false">返 回</el-button>
                <el-button type="primary" @click="addLabel">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                tableData: [],
                search: '',
                uploadVisible:false,
                imageUrl:'',
                form:{
                    labelName:'',
                }
            }
        },
        created(){
            this.getData();
        },
        methods: {
            getData(){
                var that=this;
                this.$axios
                    .get(this.$baseUrl+"/manage/getAllLabel")
                    .then(function (res) {
                        that.tableData=res.data.data;
                    })
            },
            saveEdit(row){
                var that=this;
                this.$axios
                    .post(this.$baseUrl+"/manage/changeLabel",{"labelsId":row.labelsId,"labelsName":row.labelsName})
                    .then(function (res) {
                        that.$message.success("保存成功");
                        that.getData();
                    })
            },
            addLabel(){
                if(this.form.labelName===''){
                    this.$message.error("请输入标签名称")
                } else {
                    if(this.imageUrl===''){
                        this.$message.error("请选择标签图标")
                    } else {
                        this.uploadFile();
                        this.uploadVisible=false;
                        this.$message.success("新增成功");
                    }
                }
            },
            uploadFile() {
                const file = this.$refs.upload.uploadFiles[0];
                var data;
                const isLt2M = file.size / 1024 / 1024 < 2;
                if (!isLt2M) {
                    this.$message.error("请上传2M以下的.jpg文件");
                    return false;
                }
                this.imageUrl = URL.createObjectURL(file.raw);
                // 发起请求
                const headerConfig = { headers: { 'Content-Type': 'application/json' } };
                var reader = new FileReader();
                var that=this;
                reader.readAsDataURL(file.raw);
                reader.onload = function(e) {
                    data = this.result; // 这个就是base64编码了
                    console.log(data);
                    that.$axios
                        .post(that.$baseUrl+"/manage/addLabel",
                            {"labelsName": that.form.labelName,
                                "labelsUrl":data},headerConfig)
                        .then(res=>{
                            if (res.status===200){
                                that.$message.success("上传成功");
                                that.getData();
                            }else {
                                that.$message.error("上传失败")
                            }
                        });
                };
            },
            //图片展示
            changeUpload (file, fileList){
                this.imageUrl=URL.createObjectURL(file.raw);
            },
            handleUpload(){
                this.form={
                    labelName:''
                };
                this.imageUrl='';
                this.uploadVisible=true;
            },
            handleDelete(row) {
                var that=this;
                this.$confirm('此操作将永久删除信息会影响客户体验, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    console.log(row.labelsId)
                    this.$axios
                        .post(this.$baseUrl+'/manage/deleteLabel',{"labelsId":row.labelsId})
                        .then(function (res) {
                            that.$message.success("删除成功");
                            that.getData();
                        })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            }
        },
        computed:{
        }
    }
</script>

<style>
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }
    .avatar-uploader .el-upload:hover {
        border-color: #409EFF;
    }
    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 178px;
        height: 178px;
        line-height: 178px;
        text-align: center;
    }
    .avatar {
        width: 178px;
        height: 178px;
        display: block;
    }
    .pic{
        width: 50px;
        height: 50px;
        border-radius: 50%;
    }
    .search{
        width: 200px;
        float: right;
        text-indent: -50px;
    }
    .addTopic{
        float: left;
        text-indent: 80px;
    }

    .tb-edit .el-input {
        display: none;
    }
    el-input{
        width: 70px;
    }
    .tb-edit .current-row .el-input {
        display: block
    }
    .tb-edit .current-row .el-input+span {
        display: none
    }
    .tb-edit .current-row .el-date-picker+span {
        display: none
    }
</style>

