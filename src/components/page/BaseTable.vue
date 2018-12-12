<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-tickets"></i> 基础表格</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button>
                <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                    <el-option key="1" label="广东省" value="广东省"></el-option>
                    <el-option key="2" label="湖南省" value="湖南省"></el-option>
                </el-select>
                <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
                <el-button type="primary" icon="search" @click="search">搜索</el-button>
            </div>
            <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column prop="date" label="日期" sortable width="150">
                </el-table-column>
                <el-table-column prop="name" label="姓名" width="120">
                </el-table-column>
                <el-table-column prop="address" label="地址" :formatter="formatter">
                </el-table-column>
                <el-table-column label="操作" width="180">
                    <template slot-scope="scope">
                        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
                </el-pagination>
            </div>
        </div>

        <!-- 编辑弹出框 -->
        <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="50px">
                <el-form-item label="日期">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.date" value-format="yyyy-MM-dd" style="width: 100%;"></el-date-picker>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>

            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog>

        <!-- 删除提示框 -->
        <el-dialog title="提示" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false">取 消</el-button>
                <el-button type="primary" @click="deleteRow">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                url: './static/vuetable.json',
                tableData: [],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                del_list: [],
                is_search: false,
                editVisible: false,
                delVisible: false,
                form: {
                    name: '',
                    date: '',
                    address: ''
                },
                idx: -1
            }
        },
        created() {
            this.getData();
        },
        computed: {
            data() {
                return this.tableData.filter((d) => {
                    let is_del = false;
                    for (let i = 0; i < this.del_list.length; i++) {
                        if (d.name === this.del_list[i].name) {
                            is_del = true;
                            break;
                        }
                    }
                    if (!is_del) {
                        if (d.address.indexOf(this.select_cate) > -1 &&
                            (d.name.indexOf(this.select_word) > -1 ||
                                d.address.indexOf(this.select_word) > -1)
                        ) {
                            return d;
                        }
                    }
                })
            }
        },
        methods: {
            // 分页导航
            handleCurrentChange(val) {
                this.cur_page = val;
                this.getData();
            },
            // 获取 easy-mock 的模拟数据
            getData() {
                // 开发环境使用 easy-mock 数据，正式环境使用 json 文件
                if (process.env.NODE_ENV === 'development') {
                    this.url = '/ms/table/list';
                };
                this.$axios.post(this.url, {
                    page: this.cur_page
                }).then((res) => {
                    this.tableData = res.data.list;
                })
            },
            search() {
                this.is_search = true;
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            handleEdit(index, row) {
                this.idx = index;
                const item = this.tableData[index];
                this.form = {
                    name: item.name,
                    date: item.date,
                    address: item.address
                }
                this.editVisible = true;
            },
            handleDelete(index, row) {
                this.idx = index;
                this.delVisible = true;
            },
            delAll() {
                const length = this.multipleSelection.length;
                let str = '';
                this.del_list = this.del_list.concat(this.multipleSelection);
                for (let i = 0; i < length; i++) {
                    str += this.multipleSelection[i].name + ' ';
                }
                this.$message.error('删除了' + str);
                this.multipleSelection = [];
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            // 保存编辑
            saveEdit() {
                this.$set(this.tableData, this.idx, this.form);
                this.editVisible = false;
                this.$message.success(`修改第 ${this.idx+1} 行成功`);
            },
            // 确定删除
            deleteRow(){
                this.tableData.splice(this.idx, 1);
                this.$message.success('删除成功');
                this.delVisible = false;
            }
        }
    }
</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
</style>

<!--<template>-->
    <!--<div>-->
        <!--&lt;!&ndash;<div class="handle-box">&ndash;&gt;-->
            <!--&lt;!&ndash;<el-button type="success" @click="addOneMsg">增加一行</el-button>&ndash;&gt;-->
        <!--&lt;!&ndash;</div>&ndash;&gt;-->
        <!--&lt;!&ndash;<el-table :data="tableData" class="tb-edit" style="width: 100%"&ndash;&gt;-->
                  <!--&lt;!&ndash;highlight-current-row @row-click="handleCurrentChange">&ndash;&gt;-->
            <!--&lt;!&ndash;<el-table-column label="日期" width="180">&ndash;&gt;-->
                <!--&lt;!&ndash;<template slot-scope="scope">&ndash;&gt;-->
                    <!--&lt;!&ndash;<el-input size="small" v-model="scope.row.date" placeholder="请输入内容" @change="handleEdit(scope.$index, scope.row)"></el-input>&ndash;&gt;-->
                    <!--&lt;!&ndash;<span>{{scope.row.date}}</span>&ndash;&gt;-->
                <!--&lt;!&ndash;</template>&ndash;&gt;-->
            <!--&lt;!&ndash;</el-table-column>&ndash;&gt;-->
            <!--&lt;!&ndash;<el-table-column prop="" label="排版">&ndash;&gt;-->
            <!--&lt;!&ndash;</el-table-column>&ndash;&gt;-->
            <!--&lt;!&ndash;<el-table-column label="姓名" width="180">&ndash;&gt;-->
                <!--&lt;!&ndash;<template slot-scope="scope">&ndash;&gt;-->
                    <!--&lt;!&ndash;<el-input size="small" v-model="scope.row.name" placeholder="请输入内容" @change="handleEdit(scope.$index, scope.row)"></el-input> <span>{{scope.row.name}}</span>&ndash;&gt;-->
                <!--&lt;!&ndash;</template>&ndash;&gt;-->
            <!--&lt;!&ndash;</el-table-column>&ndash;&gt;-->
            <!--&lt;!&ndash;<el-table-column prop="address" label="地址">&ndash;&gt;-->
                <!--&lt;!&ndash;<template slot-scope="scope">&ndash;&gt;-->
                    <!--&lt;!&ndash;<el-input size="small" v-model="scope.row.address" placeholder="请输入内容" @change="handleEdit(scope.$index, scope.row)"></el-input> <span>{{scope.row.address}}</span>&ndash;&gt;-->
                <!--&lt;!&ndash;</template>&ndash;&gt;-->
            <!--&lt;!&ndash;</el-table-column>&ndash;&gt;-->
        <!--&lt;!&ndash;</el-table>&ndash;&gt;-->
        <!--&lt;!&ndash;<br>数据:{{tableData}}&ndash;&gt;-->

        <!--<el-form-item ref="videocontentvideo" style="display:none;">-->
            <!--<img :src="imgurl">-->
            <!--<video width="320" height="240" controls id="upvideo">-->
            <!--</video>-->
            <!--<img :src="modlevidel" />-->
        <!--</el-form-item>-->

        <!--<el-form-item class="upload-demo-content" v-show="formLabelAlign.type===2">-->
            <!--<el-upload class="upload-demo"-->
                       <!--:limit="1"-->
                       <!--:on-exceed="exceedhandle" drag-->
                       <!--:file-list="filsListArray"-->
                       <!--:action="configuploadurl"-->
                       <!--:on-success="successuploadhandle"-->
                       <!--accept="video"-->
                       <!--name="fileList"-->
                       <!--:before-upload="beforeUploadVideo">-->
                <!--<i class="el-icon-upload"></i>-->
                <!--<div class="el-upload__text">将视频文件拖到此处，或-->
                    <!--<em>点击上传</em>-->
                <!--</div>-->
                <!--<div class="el-upload__tip" slot="tip"></div>-->
            <!--</el-upload>-->
        <!--</el-form-item>-->

    <!--</div>-->
<!--</template>-->
<!--<script>-->
    <!--export default {-->
        <!--data(){-->
            <!--return{-->
                <!--tableData: [{-->
                    <!--date: '2016-05-02',-->
                    <!--name: '王小虎',-->
                    <!--address: '上海市普陀区金沙江路 1518 弄'-->
                <!--}, {-->
                    <!--date: '2016-05-04',-->
                    <!--name: '王小虎',-->
                    <!--address: '上海市普陀区金沙江路 1517 弄'-->
                <!--}, {-->
                    <!--date: '2016-05-01',-->
                    <!--name: '王小虎',-->
                    <!--address: '上海市普陀区金沙江路 1519 弄'-->
                <!--}, {-->
                    <!--date: '2016-05-03',-->
                    <!--name: '王小虎',-->
                    <!--address: '上海市普陀区金沙江路 1516 弄'-->
                <!--}]-->
            <!--}-->
        <!--},-->
        <!--methods: {-->
            <!--handleCurrentChange(row, event, column) {-->
                <!--console.log(row, event, column, event.currentTarget)-->
            <!--},-->
            <!--handleEdit(index, row) {-->
                <!--console.log(index, row);-->
            <!--},-->
            <!--addOneMsg(){-->
                <!--this.tableData.push({-->
                    <!--date: '',-->
                    <!--name: '',-->
                    <!--address: ''-->
                <!--})-->
            <!--},-->
            <!--successuploadhandle(response, file, fileList) {-->
                <!--this.formLabelAlign.video = this.modlevidel =-->
                    <!--response.data.fileList[0].url;-->
                <!--this.filsListArray = [];-->
                <!--this.filsListArray.push({-->
                    <!--name: this.formLabelAlign.video,-->
                    <!--url: this.formLabelAlign.video-->
                <!--});-->
                <!--this.findvideocover();-->
            <!--},-->
            <!--exceedhandle(files, fileList) {-->
                <!--this.$message.error("只能上传一个视频额");-->
            <!--},-->
            <!--beforeUploadVideo(file) {-->
                <!--if (-->
                    <!--[-->
                        <!--"video/mp4",-->
                        <!--"video/ogg",-->
                        <!--"video/flv",-->
                        <!--"video/avi",-->
                        <!--"video/wmv",-->
                        <!--"video/rmvb"-->
                    <!--].indexOf(file.type) === -1-->
                <!--) {-->
                    <!--this.$message.error("请上传正确的视频格式");-->
                    <!--return false;-->
                <!--}-->
            <!--},-->
            <!--findvideocover() {-->
                <!--let _this = this;-->
                <!--this.$nextTick(() => {-->
                    <!--let video = document.getElementById("upvideo");-->
                    <!--let source = document.createElement("source");-->
                    <!--// source.src = require("../../assets/5b086751dbb7af1ea8fa8d05e66fe5c3.mp4");this.formLabelAlign.video-->
                    <!--source.src = this.formLabelAlign.video;-->
                    <!--source.type = "video/mp4";-->
                    <!--video.appendChild(source);-->
                    <!--video.addEventListener("loadeddata", function() {-->
                        <!--var canvas = document.createElement("canvas");-->
                        <!--canvas.width = "320";-->
                        <!--canvas.height = "320";-->
                        <!--canvas-->
                            <!--.getContext("2d")-->
                            <!--.drawImage(video, 0, 0, canvas.width, canvas.width);-->
                        <!--var img = document.createElement("img");-->
                        <!--let imgsrc = canvas.toDataURL("image/png");-->
                        <!--_this.Videoframehandle(imgsrc.split(",")[1]);-->
                    <!--});-->
                <!--});-->
            <!--},-->
            <!--showvideohandle() {-->
                <!--this.$alert(-->
                    <!--`<video width="320" height="240" controls>-->
                      <!--<source src="${-->
                        <!--this.filsListArray[0].url-->
                        <!--}"  type="video/mp4">-->
                      <!--您的浏览器不支持 HTML5 video 标签。-->
                    <!--</video>`,-->
                    <!--"视频预览",-->
                    <!--{-->
                        <!--dangerouslyUseHTMLString: true-->
                    <!--}-->
                <!--);-->
            <!--},-->
        <!--}-->
    <!--}-->
<!--</script>-->
<!--<style>-->
    <!--.tb-edit .el-input {-->
        <!--display: none-->
    <!--}-->
    <!--.tb-edit .current-row .el-input {-->
        <!--display: block-->
    <!--}-->
    <!--.tb-edit .current-row .el-input+span {-->
        <!--display: none-->
    <!--}-->
    <!--.handle-box{-->
        <!--margin-bottom: 20px;-->
    <!--}-->
<!--</style>-->
