<template>
    <div class="exceptionQuery">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>质量查询</el-breadcrumb-item>
                <el-breadcrumb-item>不具合查询与统计</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="exceptionQueryContent">
            <div class="exceptionQueryContentTab">
                <div class="selectTab">
                    <el-select v-model="gxType" placeholder="请选择工序">
                        <el-option
                            v-for="item in gxTypeOptions"
                            :key="item.id"
                            :label="item.name"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </div>
                <div class="selectTab">
                    <el-select v-model="ycType" placeholder="请选择异常类型">
                        <el-option
                            v-for="item in ycTypeOptions"
                            :key="item.indexno"
                            :label="item.name"
                            :value="item.indexno">
                        </el-option>
                    </el-select>
                </div>
                <div class="selectTab">
                    <el-select v-model="gzType" placeholder="选择当前管状态">
                        <el-option
                            v-for="item in gzTypeOptions"
                            :key="item.indexno"
                            :label="item.name"
                            :value="item.indexno">
                        </el-option>
                    </el-select>
                </div>
                <div class="selectTab">
                    <el-button type="primary" @click="doSearch">查询</el-button>
                </div>
            </div>
            <div class="exceptionQueryTable">
                <el-table
                    :data="tableData"
                    :header-cell-style="{background:'#f7f7f7',color:'rgba(0, 0, 0, 1)',fontSize:'14px'}"
                    border
                    @row-click="clickTable"
                    style="width: 98%;margin: 0 auto">

                    <el-table-column
                        prop="stationname"
                        label="工序名称"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="type"
                        label="异常类型"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="zhuangtai"
                        label="管子状态"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="yichangxinxi"
                        label="录入原因"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="zerenren"
                        label="责任人"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="chuliduiche"
                        label="处理对策"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="shunshigongsi"
                        label="损失工时"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="cailiaoqingkuang"
                        label="损失材料"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="chulijieguo"
                        label="处理结果"
                        align="center"
                        min-width="20%">
                    </el-table-column>
                    <el-table-column
                        prop="chulishijian"
                        label="处理时间"
                        align="center"
                        min-width="20%">
                    </el-table-column>

                </el-table>
            </div>
        </div>
        <!-- 编辑弹出框 -->
        <el-dialog title="处理异常" :visible.sync="editVisible" width="70%">
            <el-form ref="form"  label-width="100px">
                <el-form-item label="录入原因" >
                    <el-input v-model="yuanyin"></el-input>
                </el-form-item>
                <el-form-item label="责任人">
                    <el-input v-model="zerenren"></el-input>
                </el-form-item>
                <el-form-item label="处理对策">
                    <el-input v-model="chuliduice"></el-input>
                </el-form-item>
                <el-form-item label="损失工时">
                    <el-input v-model="sunshigongshi" type="number"  step="0.1"></el-input>
                </el-form-item>
                <el-form-item label="材料情况">
                    <el-input v-model="cailiaoqingkuang" ></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false" style="height:30px;width:80px">取 消</el-button>
                <el-button type="primary" @click="saveEdit" style="height:30px;width:80px">确 定</el-button>
            </span>
        </el-dialog>
        <Modal :msg="message"
               :isHideModal="HideModal"></Modal>
    </div>
</template>
<script type="text/ecmascript-6">
    import axios from 'axios'
    import Modal from '../../common/modal'
    import url from '../../assets/js/URL'

    export default {
        name: 'FactoryCalendar',
        data() {
            return {
                message: '',
                HideModal: true,
                num: 0,
                batch: "",
                username:"",

                gzId:"",
                tableData: [],

                gxType: '',
                gxTypeOptions: [],

                ycType: '',
                ycTypeOptions: [],

                gzType: '',
                gzTypeOptions: [],

                editVisible: false,

                yuanyin: "",
                zerenren: '',
                chuliduice: '',
                sunshigongshi: '',
                cailiaoqingkuang: ""


            }
        },
        components: {Modal},
        mounted() {


        },
        created() {
            this.getAdminState()

        },
        methods: {

            //页面加载检查用户是否登陆，没有登陆就加载登陆页面
            getAdminState() {
                const userInfo = localStorage.getItem("userInfo");
                if (userInfo === null) {
                    this.$router.push("/")
                }
                else {
                    let Info = JSON.parse(userInfo);
                    this.username = Info.username;
                    let that = this;
                    axios.all([
                        axios.post(" " + url + "/api/getProcessList.html"),
                        axios.post(" " + url + "/sys/dictionaryList", {"id": 1}),
                        axios.post(" " + url + "/sys/dictionaryList", {"id": 3})
                    ])
                        .then(axios.spread(function (gx, yc, gz) {
                           that.gxTypeOptions = gx.data;
                            that.ycTypeOptions = yc.data;
                            that.gzTypeOptions = gz.data;
                        }));
                }
            },

            //进行查询
            doSearch() {
                if (this.gxType && this.ycType && this.gzType) {
                    axios.post(" " + url + "/shengchanError/errorEventList", {
                        "id": this.gxType,
                        "errorId": this.ycType,
                        "status": this.gzType
                    })
                        .then((res) => {
                            this.tableData = res.data;
                        })
                        .catch((err) => {
                            console.log(err)
                        })

                }
                else {
                    this.message = "请完善查询信息";
                    this.HideModal = false;
                    const that = this;

                    function a() {
                        that.message = "";
                        that.HideModal = true;
                    }

                    setTimeout(a, 2000);
                }

            },

            clickTable(e) {
                this.gzId =e.id;
                this.editVisible = true;
            },


            // 保存编辑
            saveEdit() {
                if (this.yuanyin && this.zerenren && this.chuliduice && this.sunshigongshi && this.cailiaoqingkuang) {
                    axios.post(" " + url + "/shengchanError/curErrorEvent", {
                        "userId": this.username,
                        "id": this.gzId,
                        "context": this.yuanyin,
                        "zerenren": this.zerenren,
                        "chuliduiche": this.chuliduice,
                        "shunshigongsi": this.sunshigongshi,
                        "status":1 ,
                    })
                        .then((res) => {
                            if (res.data === "1") {
                                axios.post(" " + url + "/shengchanError/errorEventList", {
                                    "id": this.gxType,
                                    "errorId": this.ycType,
                                    "status": this.gzType
                                })
                                    .then((res) => {
                                        this.editVisible = false;
                                        this.$message.success(`提交第成功`);
                                        this.tableData = res.data;
                                    })
                                    .catch((err) => {
                                        console.log(err)
                                    })
                            }
                        })
                        .catch((err) => {
                            console.log(err)
                        })
                }
                else {
                    this.$message.warning(`输入框不能为空，请正确填写`);
                }
            },
        }
    }
</script>
<style scoped lang="less" rel="stylesheet/less">
    @import "../../assets/less/base";
    .exceptionQuery{
        width: 100%;
        height: 100%;
        background-color: @color-white;
        .crumbs{
            height: 50px;
            padding-top: 20px;
            padding-left: 20px;
        }
        .exceptionQueryContent {
            .exceptionQueryContentTab {
                height: 100px;
                display: flex;
                border-bottom: 1px solid @color-background-d;
                .selectTab {
                    flex: 1;
                    display: flex;
                    align-items: center;
                    justify-content: center;
                    button {
                        width: 100px;
                        height: 30px;
                    }
                }
            }
            .exceptionQueryTable {
                padding-top: 10px;
                height: 450px;
                padding-bottom: 10px;
                overflow-y: auto;
            }
        }

    }



</style>
