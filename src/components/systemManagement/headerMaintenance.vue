<template>
    <div class="headerMaintenance">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>系统管理</el-breadcrumb-item>
                <el-breadcrumb-item>表头维护</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="headerMaintenance-content">
            <div class="container">
                <div class="handle-box">
                    <el-input v-model="select_word" placeholder="筛选字典" class="handle-input mr10"></el-input>
                    <el-select
                        v-model="select"
                        clearable
                        filterable
                        allow-create
                        default-first-option
                        placeholder="请选择字典类型">
                        <el-option
                            v-for="item in selectOptions"
                            :key="item.code"
                            :label="item.name"
                            :value="item.code">
                        </el-option>
                    </el-select>
                    <el-button type="success" icon="delete" class="handle-del mr10" @click="doSearch">查询表头</el-button>
                    <el-button type="primary" icon="delete" class="handle-del mr10" @click="showAddPerson">新增表头</el-button>
                    <el-button type="danger" icon="delete" class="handle-del mr10" @click="deletePerson">删除表头</el-button>
                </div>
                <div class="">
                    <el-table class="tb-edit"
                              :data="tables"
                              :header-cell-style="{background:'#f7f7f7',color:'rgba(0, 0, 0, 1)',fontSize:'14px'}"
                              border
                              @select="selectList"
                              @row-dblclick="editPerson"
                              highlight-current-row
                              style="width: 98%;margin: auto">
                        <el-table-column
                            type="selection"
                            width="30">
                        </el-table-column>
                        <template v-for="(col ,index) in cols">
                            <el-table-column align="center" :prop="col.prop" :label="col.label"></el-table-column>
                        </template>
                    </el-table>
                </div>
            </div>
        </div>
        <!--新增弹出框 -->
        <el-dialog title="新增表头" :visible.sync="addVisible" width="60%">
            <el-form ref="form" label-width="100px">
                <el-form-item label="表头代码">
                    <el-select
                        v-model="select"
                        clearable
                        filterable
                        allow-create
                        default-first-option
                        placeholder="请选择字典类型">
                        <el-option
                            v-for="item in selectOptions"
                            :key="item.code"
                            :label="item.name"
                            :value="item.code">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="属性代码">
                    <el-input v-model="pro"></el-input>
                </el-form-item>
                <el-form-item label="属性显示名">
                    <el-input v-model="label"></el-input>
                </el-form-item>
                <el-form-item label="顺序号">
                    <el-input v-model="sortnum"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="addVisible = false" style="height:30px;width:80px">取 消</el-button>
                <el-button type="primary" @click="doAddPerson" style="height:30px;width:80px">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 编辑弹出框 -->
        <el-dialog title="编辑表头" :visible.sync="editVisible" width="60%">
            <el-form ref="form" label-width="100px">
                <el-form-item label="表头代码">
                    <el-select
                        v-model="select"
                        clearable
                        filterable
                        allow-create
                        default-first-option
                        placeholder="请选择字典类型">
                        <el-option
                            v-for="item in selectOptions"
                            :key="item.code"
                            :label="item.name"
                            :value="item.code">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="属性代码">
                    <el-input v-model="pro"></el-input>
                </el-form-item>
                <el-form-item label="属性显示名">
                    <el-input v-model="label"></el-input>
                </el-form-item>
                <el-form-item label="顺序号">
                    <el-input v-model="sortnum"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false" style="height:30px;width:80px">取 消</el-button>
                <el-button type="primary" @click="saveEdit" style="height:30px;width:80px">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 删除提示框 -->
        <el-dialog title="删除表头" :visible.sync="delVisible" width="300px" center>
            <div class="del-dialog-cnt">删除不可恢复，是否确定删除？</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="delVisible = false" style="height:30px;width:80px">取 消</el-button>
                <el-button type="primary" @click="deleteRow" style="height:30px;width:80px">确 定</el-button>
            </span>
        </el-dialog>
        <Modal :msg="message"
               :isHideModal="HideModal"></Modal>
    </div>
</template>
<script type="text/ecmascript-6">
    import axios from 'axios'
    import url from '../../assets/js/URL'
    import Modal from '../../common/modal'
    export default {
        name: 'WorkingProcedure',
        data() {
            return {
                message: '',
                HideModal: true,
                listData: [],


                cols: [],
                tableData: [],

                select_word: '',

                addVisible: false,
                editVisible: false,
                delVisible: false,



                select:"",
                selectOptions: [],

                id: "",
                pro: '',
                label: '',
                sortnum: '',


            }
        },
        computed: {
            //模糊检索
            tables: function () {
                var search = this.select_word;
                if (search) {
                    return this.tableData.filter(function (dataNews) {
                        return Object.keys(dataNews).some(function (key) {
                            return String(dataNews[key]).indexOf(search) > -1
                        })
                    })
                }
                return this.tableData
            }
        },
        components: {Modal},
        mounted() {


        },
        created() {
            this.getAdminState();
        },
        methods: {

            //页面加载检查用户是否登陆，没有登陆就加载登陆页面
            getAdminState() {
                const userInfo = localStorage.getItem("userInfo");
                if (userInfo === null) {
                    this.$router.push("/")
                }
                else {
                    let that = this;
                    axios.all([
                        axios.post(" " + url + "/sys/dictionaryList", {"id": "7"}),
                        axios.post(" " + url + "/sys/showTableTitle", {"name": "tabletitle"}),
                        axios.post(" " + url + "/sysconfig/tableTitleList",{"code":"qieduan"})
                    ])
                        .then(axios.spread(function (select,title, table) {
                            that.selectOptions = select.data;
                            that.cols = title.data;
                            that.tableData = table.data;
                        }));
                }
            },
            //查询表头
            doSearch(){
                if (this.select) {
                    let that = this;
                    axios.all([
                        axios.post(" " + url + "/sys/showTableTitle", {"name": "tabletitle"}),
                        axios.post(" " + url + "/sysconfig/tableTitleList",{"code":this.select})
                    ])
                        .then(axios.spread(function (title, table) {
                            that.cols = title.data;
                            that.tableData = table.data;
                        }));
                }
                else {
                    this.message = "请选择字典类型";
                    this.HideModal = false;
                    const that = this;

                    function a() {
                        that.message = "";
                        that.HideModal = true;
                    }

                    setTimeout(a, 2000);
                }

            },



            //选择那个一个
            selectList(val) {
                if (val.length) {
                    let data = [];
                    for (let i = 0; i < val.length; i++) {
                        let a = val[i].id;
                        data.push(a)
                    }
                    this.listData = data;
                }
                else {
                    this.listData = [];
                }
            },
            //双击点击行内编辑
            editPerson(row, column, cell, event) {
                this.editVisible = true;
                this.id = row.id;
                axios.post(" " + url + "/sysconfig/tableTitleDetail", {"id": this.id})
                    .then((res) => {
                        this.pro = res.data.pro;
                        this.label = res.data.label;
                        this.sortnum = res.data.sortnum;
                    })
                    .catch((err) => {
                        console.log(err)
                    });
            },
            //选择点击删除
            deletePerson() {
                if (this.listData.length) {
                    this.delVisible = true;
                }
                else {
                    this.message = "请勾选要删除的人员";
                    this.HideModal = false;
                    const that = this;

                    function a() {
                        that.message = "";
                        that.HideModal = true;
                    }

                    setTimeout(a, 2000);
                }
            },
            // 保存编辑
            saveEdit() {
                if (this.pro && this.label && this.select && this.sortnum) {
                    axios.post(" " + url + "/sysconfig/updateTableTitle",
                        {
                            "id":this.id,
                            "pro": this.pro,
                            "label": this.label,
                            "code": this.select,
                            "sortnum": this.sortnum
                        }
                    )
                        .then((res) => {
                            if (res.data == "1") {
                                this.$message.success(`修改成功`);
                                this.editVisible = false;
                                const that = this;
                                setTimeout(function () {
                                    that.$router.go(0)
                                }, 1500)
                            }
                            else {
                                this.$message.warning(`修改失败`);
                            }
                        })
                        .catch((err) => {
                            console.log(err)
                        })
                }
                else {
                    this.$message.warning(`输入不能为空`);
                }

            },
            // 确定删除
            deleteRow() {
                axios.post(" " + url + "/sysconfig/delTableTitle",
                    {
                        "ids": this.listData,
                    }
                )
                    .then((res) => {
                        if (res.data === "1") {
                            this.$message.success('删除成功');
                            this.delVisible = false;
                            let that =this;
                            setTimeout(function () {
                                that.$router.go(0)
                            }, 1500)
                        }
                        else {
                            this.$message.warning(`删除失败`);
                        }
                    })
                    .catch((err) => {
                        console.log(err)
                    })
            },
            //显示新增表头
            showAddPerson() {
                this.addVisible = true;
            },
            //新增表头
            doAddPerson() {
                if (this.pro && this.label && this.select && this.sortnum) {
                    axios.post(" " + url + "/sysconfig/TableTitleAdd",
                        {
                            "pro": this.pro,
                            "label": this.label,
                            "code":  this.select,
                            "sortnum": this.sortnum
                        }
                    )
                        .then((res) => {
                            if (res.data === "1") {
                                this.$message.success(`新增成功`);
                                this.editVisible = false;
                                let that = this;
                                setTimeout(function () {
                                    that.$router.go(0)
                                }, 1500)
                            }
                            else {
                                this.$message.warning(`新增失败`);
                            }
                        })
                        .catch((err) => {
                            console.log(err)
                        })
                }
                else {
                    this.$message.warning(`输入不能为空`);
                }
            }
        }
    }
</script>
<style scoped lang="less" rel="stylesheet/less">
    @import "../../assets/less/base";

    .headerMaintenance {
        width: 100%;
        height: 100%;
        background-color: @color-white;
        .crumbs {
            height: 50px;
            padding-top: 20px;
            padding-left: 20px;
        }
        .headerMaintenance-content {
            padding-top: 10px;
            height: 450px;
            padding-bottom: 10px;
            overflow-y: auto;
            .handle-box {
                height: 80px;
                line-height: 80px;
                padding-left: 50px;
                .handle-input {
                    width: 300px;
                    display: inline-block;
                }
                .el-button {
                    width: 100px;
                    height: 30px;
                }
            }
            .del-dialog-cnt {
                font-size: 16px;
                text-align: center
            }
            .table {
                width: 100%;
                font-size: 14px;
            }
            .red {
                color: #ff0000;
            }

        }
    }


</style>
