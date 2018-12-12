<template>
<div id="header" class="header navbar navbar-inverse navbar-fixed-top">
    <!-- begin container-fluid -->
    <div class="container-fluid">
        <!-- begin mobile sidebar expand / collapse button -->
        <div class="navbar-header" @click="pageHome">
            <a href="/nca/homepage/NCAHomePageControl" class="navbar-brand">
                <!--<span class="navbar-logo"></span>-->
                <img v-show="tenant==='nca'" src="/static/images/nca-logo.png" style="width: 80%;max-height: 300px;margin-top: -7px;" alt="" />
                <img  v-show="tenant==='erc'" src="/static/images/erc-logo.png" style="width: 126px;height: 60px;margin-top: -16px;margin-left: 25px" alt="" />
            </a>
            <button type="button" class="navbar-toggle" data-click="sidebar-toggled">
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            </button>
        </div>
        <!-- end mobile sidebar expand / collapse button -->

        <!-- begin header navigation right -->
        <ul class="nav navbar-nav navbar-right">
            <li class="dropdown navbar-user">
                <a href="javascript:;" class="dropdown-toggle" data-toggle="dropdown" aria-expanded="false">
                    <img :src="userinfo.avatar" alt="" />
                    <span class="hidden-xs">{{userinfo.name}}</span><b class="caret"></b>
                </a>
                <ul class="dropdown-menu animated fadeInLeft">
                    <li class="arrow"></li>
                    <li>
                        <router-link to="/common/system/UserSetting">设置</router-link>
                    </li>
                    <li class="divider"></li>
                    <li><a href="javascript:void(0)" v-on:click="logout" style="padding-top: 0px">签退</a></li>
                </ul>
            </li>
        </ul>
        <!-- end header navigation right -->
    </div>
    <!-- end container-fluid -->
</div>
</template>
<script>
let common = require('commonFunc')
export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            tenant:common.getStoreData('tenant')
        }
    },
    name: 'mainHeader',
    mounted: function() {
        let _self = this;
        common.setTitle(_self.tenant);
    },
    methods: {
        pageHome: function(event) {
            if (this.userinfo.domain_type === '0') {
                this.$router.push({
                    path: '/nca/homepage/NCAHomePageControl'
                })
            } else if (this.userinfo.domain_type === '1') {
                this.$router.push({
                    path: '/nca/homepage/NCAHomePageControl'
                })
            } else if (this.userinfo.domain_type === '2') {
                this.$router.push({
                    path: '/nca/homepage/NCAHomePageControl'
                })
            }
        },
        logout: async function(event) {
            common.clearStoreData()
            this.$http.post('/api/signout', {})
            this.$router.push({
                path: '/login'
            })
        }
    }
}
</script>
<style scoped>
    .avatar-position {
        position: absolute;
        top: 15px;
        left: -35px
    }
</style>
