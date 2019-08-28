<template>
    <div class="default">
        <el-container>
            <el-aside width="250px">
                <el-row class="logo-image">
                    <el-col :span="24">
                        <img src="@/assets/logo.jpg" alt>
                    </el-col>
                </el-row>
                <div class="wrapper">
                    <el-menu
                            :router="true"
                            unique-opened
                            :default-active="$route.path"
                            class="el-menu-vertical-demo"
                            background-color="#FFFFFF"
                            text-color="#475780"
                            active-text-color="#118DEE"
                    >
                        <!--:default-active="$route.path" @open="handleOpen"-->
                        <el-menu-item index="/home" v-has="{class:'home'}">
                            <i class="fa fa-home"></i>
                            <span slot="title">首页</span>
                        </el-menu-item>
                        <el-submenu index="/goods/goodslist" v-has="{class:'goods'}">
                            <template slot="title">
                                <i class="fa fa-shopping-bag"></i>
                                <span>商品管理</span>
                            </template>
                            <el-menu-item-group>
                                <el-menu-item index="/goods/goodslist" v-has="{class:'goods_lb'}">商品列表</el-menu-item>
                                <el-menu-item index="/goods/goodsadd" v-has="{class:'goods_add'}">添加商品</el-menu-item>
                                <el-menu-item index="/goods/recyclebin" v-has="{class:'goods_hsz'}">商品回收站</el-menu-item>
                                <el-menu-item index="/goods/comment" v-has="{class:'goods_pl'}">商品评论</el-menu-item>
                                <el-menu-item index="/goods/classification" v-has="{class:'goods_fl'}">商品分类</el-menu-item>
                                <el-menu-item index="/goods/platformClass" v-has="{class:'goods_fl'}">平台商品分类</el-menu-item>
                                <el-menu-item index="/goods/picturelibrary" v-has="{class:'photo'}">图片库管理</el-menu-item>
                                <el-menu-item index="/goods/brand" v-has="{class:'brands'}">品牌管理</el-menu-item>
                            </el-menu-item-group>
                        </el-submenu>
                        <el-submenu index="/order" v-has="{class:'orders'}">
                            <template slot="title">
                                <i class="fa fa-file-text-o"></i>
                                <span>订单管理</span>
                            </template>
                            <el-menu-item index="/order/orderlist" v-has="{class:'order_list'}">
                                <span slot="title">订单列表</span>
                            </el-menu-item>
                            <el-menu-item index="/order/refundlist" v-has="{class:'order_refund'}">
                                <span slot="title">售后列表</span>
                            </el-menu-item>
                        </el-submenu>
                        <el-menu-item index="/client/clientlist" v-has="{class:'custom'}">
                            <i class="fa fa-user"></i>
                            <span slot="title">用户管理</span>
                        </el-menu-item>
                        <el-submenu index="/statistics/overview" v-has="{class:'rpt'}">
                            <template slot="title">
                                <i class="fa fa-bar-chart"></i>
                                <span>统计管理</span>
                            </template>
                            <el-menu-item-group>
                                <el-menu-item index="/statistics/overview" v-has="{class:'rpt_sjzl'}">数据总览
                                </el-menu-item>
                                <el-menu-item index="/statistics/commodity" v-has="{class:'rpt_sptj'}">商品统计
                                </el-menu-item>
                            </el-menu-item-group>
                        </el-submenu>
                        <el-submenu index="/rights/department" v-has="{class:'sys'}">
                            <template slot="title">
                                <i class="fa fa-unlock-alt"></i>
                                <span>权限管理</span>
                            </template>
                            <el-menu-item-group>
                                <el-menu-item index="/rights/department" v-has="{class:'sys_org'}">部门管理</el-menu-item>
                                <el-menu-item index="/rights/roles" v-has="{class:'sys_role'}">角色管理</el-menu-item>
                                <el-menu-item index="/rights/members" v-has="{class:'sys_user'}">成员管理</el-menu-item>
                                <el-menu-item index="/rights/logs" v-has="{class:'sys_log'}">操作日志</el-menu-item>
                                <el-menu-item index="/rights/shops" v-has="{class:'sys_shop'}">店铺管理</el-menu-item>
                            </el-menu-item-group>
                        </el-submenu>
                    </el-menu>
                </div>
            </el-aside>
            <el-container>
                <el-header height="80px">
                    <el-row type="flex" justify="space-between" align="middle" class="row-bg">
                        <el-col :span="12">
                            <Breadcrumb></Breadcrumb>
                        </el-col>
                        <el-col :span="12">
                            <ControlPanel></ControlPanel>
                        </el-col>
                    </el-row>
                </el-header>
                <el-main>
                    <!--<keep-alive include="orderlist,goodslist">-->
                        <router-view></router-view>
                    <!--</keep-alive>-->
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>


<script>
    import ControlPanel from "./components/ControlPanel";
    import Breadcrumb from "@/components/Breadcrumb";
    import BScroll from "better-scroll";
    // import userApi from "@/api/user.js";
    export default {
        data() {
            return {
                websock: null
            }
        },
        created: function () {
            this.initWebSocket();
        },
        destroyed: function () {
            //页面销毁时关闭长连接
            this.websocketclose();
        },
        components: {ControlPanel, Breadcrumb},
        methods: {
            // handleOpen(key) {
            //     this.$router.push({path: key});
            // },
            initWebSocket() { //初始化weosocket
                const wsuri = 'ws://114.55.140.250:8899/ymhsm/getAuths/' + sessionStorage.getItem('token');//ws地址
                this.websock = new WebSocket(wsuri);
                this.websock.onopen = this.websocketonopen;
                this.websock.onerror = this.websocketonerror;
                this.websock.onmessage = this.websocketonmessage;
                this.websock.onclose = this.websocketclose;
            },

            websocketonopen() {
                console.log("WebSocket连接成功");
            },
            websocketonerror(e) { //错误
                console.log("WebSocket连接发生错误" + e.data());
            },
            websocketonmessage(e) { //数据接收
                const redata = JSON.parse(e.data);
                sessionStorage.setItem('menuAndButtons' + sessionStorage.getItem('token'), redata)
                console.log(redata);
                this.$router.go(0);

            },

            websocketsend(agentData) {//数据发送
                this.websock.send(agentData);
            },

            websocketclose() { //关闭
                console.log("connection closed ()");
            }
        },
        mounted() {
            new BScroll(".wrapper", {
                scrollY: true,
                click: true,
            });
        },
        computed: {
            //获取当前路由渲染页面菜单
            defaultActive() {
                let pathLength = this.$route.path.split("/").length;
                console.log(pathLength)
                if (pathLength >= 4) {
                    let pathArray = this.$route.path.split("/");
                    pathArray.pop();
                    let path = pathArray.join("/");
                    console.log(path)
                    return path;
                }
                else {
                    return this.$route.path;
                }
                // if (pathLength.length < 4) {
                //     let path =
                //         "/" +
                //         this.$route.path.split("/").reverse()[1] +
                //         "/" +
                //         this.$route.path.split("/").reverse()[0];
                //     return path;
                // } else {
                //     let path1 =
                //         "/" +
                //         this.$route.path.split("/").reverse()[2] +
                //         "/" +
                //         this.$route.path.split("/").reverse()[1] +
                //         "/" +
                //         this.$route.path.split("/").reverse()[0];
                //     console.log(path1)
                //     return path1;
                // }
            }
        }
        // async beforeRouteEnter(to, from, next) {
        //   const res = await userApi.getMenus();
        //   console.log(res);

        //   next(vm => {});

        // }
    };
</script>

<style lang="scss" scoped>
    .el-menu-item.is-active {
        background-color: #f5f6fa !important;
    }

    .fa {
        font-size: 16px;
        margin-right: 10px;
    }

    .default {
        height: 100%;
        .el-container {
            height: 100%;
            background: #f5f6fa;
            .el-aside {
                background: #fff;
                line-height: 200px;
                display: flex;
                flex-direction: column;
                .logo-image {
                    line-height: 0;
                    padding-bottom: 10px;
                }
                .wrapper {
                    overflow: hidden;
                    height: 100%;
                }
            }
            .el-header {
                .row-bg {
                    height: 100%;
                }
            }
            .el-main {
                color: #333;
            }
        }
    }
</style>
