<template>
    <div class="plannedProduction">
        <div class="plannedProduction-nav">
            <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal">
                <div  v-for="(item,index) in navBarData"  :class="{'navDivColor':index === num}">
                    <el-submenu :index="item.index">
                        <template slot="title">{{item.label}}</template>
                        <div class="" v-for="(item ,index) in item.children" @click=" handleSelect(item.index,item.url)">
                            <el-menu-item :index="item.index">{{item.label}}</el-menu-item>
                        </div>
                    </el-submenu>
                </div>
            </el-menu>
        </div>
        <div class="plannedProduction-content">
            <keep-alive>
                <router-view></router-view>
            </keep-alive>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">


    export default {
        name: 'plannedProduction',
        data() {
            return {
                num:0,
                navBarData: [
                    {
                        label: '资源模型',
                        index: "1",
                        children: [
                            {
                                label: '工序管理',
                                index: "1-1",
                                url: "/WorkingProcedure"
                            },
                            {
                                label: '工艺路线',
                                index: "1-2",
                                url: "/ProcessRoute"
                            },
                            {
                                label: ' 工作日历',
                                index: "1-3",
                                url: "/FactoryCalendar"
                            },
                            {
                                label: '班次管理',
                                index: "1-4",
                                url: "/ShiftManagement"
                            },
                            {
                                label: '加工能力',
                                index: "1-5",
                                url: "/404"
                            }
                        ]
                    },
                    {
                        label: '计划排产',
                        index: "2",
                        url: "/WorkingProcedure",
                        children: [
                            {
                                label: '船型设置',
                                index: "2-1",
                                url: "/ShipType"
                            },
                            {
                                label: '管加工数据导入',
                                index: "2-2",
                                url: "/InformationImport"
                            },
                            {
                                label: '特别流程管数据导入',
                                index: "2-3",
                                url: "/TBImport"
                            },
                            {
                                label: '导入数据查询',
                                index: "2-4",
                                url: "/ImportDataQuery"
                            },
                            {
                                label: '计划排产',
                                index: "2-5",
                                url: "/Production"
                            }
                        ]
                    },
                    {
                        label: '任务派发',
                        index: "3",
                        url: "/ProcessRoute",
                        children: [
                            {
                                label: '任务派发',
                                index: "3-1",
                                url: "/Distribute"
                            }
                        ]
                    },
                    {
                        label: '排产分析',
                        index: "4",
                        url: "/FactoryCalendar",
                        children: [
                            {
                                label: '排产分析',
                                index: "4-1",
                                url: "/404"
                            }
                        ]
                    },
                    {
                        label: '生产调度',
                        index: "5",
                        url: "/ResourceModel",
                        children: [
                            {
                                label: '任务调度',
                                index: "5-1",
                                url: "/TaskScheduling"
                            },
                            {
                                label: '工位监测',
                                index: "5-3",
                                url: "/404"
                            }
                        ]
                    },
                    {
                        label: ' 生产监控',
                        index: "6",
                        url: "/InformationImport",
                        children: [
                            {
                                label: ' 生产进度',
                                index: "6-1",
                                url: "/404"
                            },
                            {
                                label: '工位动态',
                                index: "6-2",
                                url: "/404"
                            },
                            {
                                label: ' 视频监控',
                                index: "6-3",
                                url: "/404"
                            }
                        ]

                    },
                    {
                        label: '任务跟踪',
                        index: "9",
                        url: "/WorksheetPlanning",
                        children: [
                            {
                                label: ' 生产进度',
                                index: "9-1",
                                url: "/ProductionSchedule"
                            },
                            {
                                label: '工位动态',
                                index: "9-2",
                                url: "/WorkstationDynamics"
                            },
                            {
                                label: ' 视频监控',
                                index: "9-3",
                                url: "/404"
                            }
                        ]
                    }
                ],
                activeIndex: '1',
            }
        },
        components: {},
        mounted() {

        },

        created() {
            this.getAdminState()
        },
        methods: {

            //页面加载检查用户是否登陆，没有登陆就加载登陆页面
            getAdminState() {
                const userInfo = sessionStorage.getItem("userInfo");
                if (userInfo === null) {

                }
                else {


                }
            },
            //点击前往那个子组件
            goToNavBar(index, url) {
                this.$router.push(url);
            },

            //点击导航前往哪一个页面
            handleSelect(index, url) {
                let Num = parseInt(index.substr(0,1));
                this.num = Num-1;
                this.$router.push(url);
            }


        }
    }
</script>
<style scoped lang="less" rel="stylesheet/less">
    @import "../../assets/less/base";

    .plannedProduction {
        width: 100%;
        height: 100%;
        background-color: @color-white;
        .plannedProduction-nav {
            width: 100%;
            height: 10%;
            .el-menu-demo {
                display: flex;
                > div {
                    flex: 1;
                    text-align: center;

                }


            }

        }
        .plannedProduction-content{
            margin-bottom: 50px;
        }

    }


    .navDivColor {
        background-color: @color-background-d;
    }

    @media only screen and (max-width: 1200px) {
        .plannedProduction {
            width: 1200px;
            .plannedProduction-nav{
                width: 1200px;
            }
            .plannedProduction-content{
                width: 1200px;
            }
        }

    }


</style>
