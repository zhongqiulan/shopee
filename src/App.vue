<template>
    <div>
        <!-- 1.0 头部 -->
        <div class="header">
            <!-- 1.0 导航栏头部 -->
            <div class="head-top">
                <div class="section">
                    <div class="left-box">
                        <span>shopee买买买</span>
                        <a target="_blank" href="#"></a>
                        <a target="_blank" href="#"></a>
                    </div>
                    <div id="menu" class="right-box">
                        <span v-show="!isLogin">
                            <router-link to="/login" class="">登录</router-link>
                            <strong>|</strong>
                            <a href="" class="">注册</a>
                            <strong>|</strong>
                        </span>
                        <span v-show="isLogin">
                            <router-link to="/vipCenter" class="">会员中心</router-link>
                            <strong>|</strong>
                            <a @click="logout">退出</a>
                            <strong>|</strong>
                        </span>
                        <router-link to="/shopcart" class="">
                            <i id="shopCartId" class="iconfont icon-cart"></i>购物车(
                            <span id="shoppingCartCount">
                                <span>{{this.$store.getters.getBuyCount}}</span>
                            </span>)</router-link>
                    </div>
                </div>
            </div>

            <!-- 2.0 导航条 -->
            <div class="head-nav">
                <div class="section">
                    <div id="menu2" class="nav-box menuhd">
                        <ul>
                            <li class="index">
                                <a href="#" class="">
                                    <span class="out" style="top: 0px;">首页</span>
                                </a>
                            </li>
                            <li class="news">
                                <a href="#" class="">
                                    <span class="out" style="top: 0px;">每日精选</span>
                                </a>
                            </li>
                            <li class="photo">
                                <a href="#" class="">
                                    <span class="out" style="top: 0px;">秒杀专区</span>
                                </a>
                            </li>
                            <li class="video">
                                <a href="#" class="">
                                    <span class="out" style="top: 0px;">阳光超市</span>
                                </a>
                            </li>
                            <li class="down">
                                <a href="#" class="">
                                    <span class="out" style="top: 0px;">会员权益</span>
                                </a>
                            </li>
                            <li class="goods">
                                <router-link to="/goodslist" class="router-link-exact-active ">
                                    <span class="out" style="top: 0px;">购物商城</span>
                                </router-link>
                            </li>
                        </ul>
                    </div>
                    <div class="search-box">
                        <div class="input-box">
                            <input id="keywords" name="keywords" type="text" onkeydown="if(event.keyCode==13){};" placeholder="输入关健字" x-webkit-speech="">
                        </div>
                        <a href="javascript:;">
                            <i class="iconfont icon-search"></i>
                        </a>
                    </div>
                </div>
            </div>
        </div>

        <!-- 2.0 中间内容【动态变化】 -->
        <router-view class="routerViewBg"></router-view>

        <!-- 3.0 尾部 -->
        <div class="footer">
            <div class="section">
                <div class="foot-nav">
                    <a href="">关于我们</a>
                    <strong>|</strong>
                    <a href="">联系我们</a>
                    <strong>|</strong>
                    <a href="">联系客服</a>
                    <strong>|</strong>
                    <a href="">合作招商</a>
                    <strong>|</strong>
                    <a href="">商家帮助</a>
                    <strong>|</strong>
                    <a href="">营销中心</a>
                    <strong>|</strong>
                    <a href="">隐私政策</a>
                </div>
                <div class="foot-box">
                    <div class="copyright">
                        <p>版权所有   shopee买买买 </p>
                        <p>公司地址： 联系电话：</p>
                        <p class="gray">Copyright © 2009-2018 itcast Corporation,All Rights Reserved.</p>
                    </div>
                    <div class="service">
                        <p>周一至周日 9:00-24:00</p>
                        <a href="#">
                            <i class="iconfont icon-phone"></i>在线客服</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<!-- scoped代表样式私有，只能在当前组件的template中用 -->
<style scoped>
    .routerViewBg {
        background-color: #f5f5f5;
    }

    /** 在组件的style中，导入的时候，要加上@ */

    @import "./statics/site/js/jqueryplugins/hoverNav/css/style.css";
</style>

<script>
    //导入【局部导入 $只能在当前组件中用】
    // import $ from 'jquery'

    //按需导入公共bus
    import { bus } from './common/commonvue'

    export default { //相当于es5 module.exports =  {}
        data() {
            return {
                isLogin: false
            }
        },
        created() {
            bus.$on('isLogin', (isLogin) => {
                this.isLogin = isLogin
            })

            //发送请求，判断用户是否登录
            this.checkLogin()
        },
        mounted() {
            $("#menu2 li a").wrapInner('<span class="out"></span>');
            $("#menu2 li a").each(function () {
                $('<span class="over">' + $(this).text() + '</span>').appendTo(this);
            });

            $("#menu2 li a").hover(function () {
                $(".out", this).stop().animate({ 'top': '48px' }, 300); // move down - hide
                $(".over", this).stop().animate({ 'top': '0px' }, 300); // move down - show

            }, function () {
                $(".out", this).stop().animate({ 'top': '0px' }, 300); // move up - show
                $(".over", this).stop().animate({ 'top': '-48px' }, 300); // move up - hide
            });
        },
        methods: {
            checkLogin() {
                const url = `site/account/islogin`

                this.$axios.get(url).then(res => {
                    if (res.data.code == 'logined') {
                        this.isLogin = true
                    } else {
                        this.isLogin = false
                    }
                })
            },
            logout() {
                this.$confirm('确认退出吗?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    const url = `site/account/logout`

                    this.$axios.get(url).then(res => {
                        if (res.data.status == 1) {//登出失败
                            this.$message.error(res.data.message);
                            return
                        } else {
                            this.isLogin = false

                            //跳转到首页
                            this.$router.push({path:'/goodslist'})
                        }
                    })
                }).catch(() => {
                    console.log("取消")
                });
            }
        }
    }
</script>