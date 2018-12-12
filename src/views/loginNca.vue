<template>
<div v-cloak>
    <div class="mainDiv">
        <!--<img src="/static/images/logo/login_bg_crm.jpg" alt="" style="width:100%;height: 100%">-->

        <!--<img src="/static/images/logo/crm_login_title.png" alt="NCA全网信息化管理系统" class="title1">-->
        <div class="title1">
            NCA全网信息化管理系统
        </div>
        <img src="/static/images/logo/supplier_login_logo.png" alt="NCA乐宜家美学整装" class="title2">
        <img src="/static/images/logo/logo2.png" alt="边框" class="title3">

        <img src="/static/images/logo/supplier_login.png" alt="拎包入住梦想家" class="title4">
        <div class="title7">
            &copy;版权所有2017&nbsp;乐宜嘉家居集团有限公司&nbsp;
            <a href="#" class="link-margin" @click="link">粤ICP备17098231号</a>
        </div>
        <!--<img src="/static/images/logo/footer.png" alt="版权" class="title7">-->
        <div class="tab-box">
            <div class="tab-wrapper">
                <!--tab section 1-->
                <input type="radio" name="tab-radio" class="tab-radio" id="tab-radio-1" checked>
                <label for="tab-radio-1" class="tab-handler tab-handler-1">
                    <img class="imgwidth" src="/static/images/erweima/android.png" alt="">
                    <div class="wenzi">
                        安卓版
                    </div>
                </label>
                <div class="tab-content tab-content-1" style="background: url('/static/images/erweima/android-bg-2.png')">
                    <div class="erweima " style="float: right">
                        <img class="erweima" src="/static/images/erweima/android-admin.png" alt="">
                        <div class="biaoti">员工APP</div>
                    </div>
                    <div class="erweima">
                        <img class="erweima" src="/static/images/erweima/android-uesr.png" alt="">
                        <div class="biaoti">业主APP</div>
                    </div>
                </div>
                <!--tab section 2-->
                <input type="radio" name="tab-radio" class="tab-radio" id="tab-radio-2">
                <label for="tab-radio-2" class="tab-handler tab-handler-2">
                    <img class="imgwidth" src="/static/images/erweima/ios.png" alt="">
                    <div class="wenzi">
                        苹果版
                    </div>
                </label>
                <div class="tab-content tab-content-2" style="background: url('/static/images/erweima/ios-bg-2.png')">
                    <div class="erweima " style="float: right">
                        <img class="erweima" src="/static/images/erweima/ios-admin.png" alt="">
                        <div class="biaoti">员工APP</div>
                    </div>
                    <div class="erweima">
                        <img class="erweima" src="/static/images/erweima/ios-user.png" alt="">
                        <div class="biaoti">业主APP
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="form">

            <input type="text" class="userName" placeholder="请输入用户名" v-model="username">
            <img src="/static/images/logo/username.png" alt="username" class="userPng">
            <input type="password" class="password" placeholder="请输入密码" v-model="password">
            <img src="/static/images/logo/password.png" alt="username" class="passPng">


            <div style="margin-top: 10px;margin-left: 15px" v-bind:class="{ 'hidden': isA }">
                <img src="/static/images/logo/err.png" alt="" style="margin-top: -4px">
                <span style="color: #ff3333;font-size: 14px;margin-left: 5px">{{ errorMessage }}</span>
            </div>

            <!--<div class="remember">记住我</div>-->
            <!--<img src="/static/images/logo/unCheck.png" alt="" class="uncheck" id="checkPng">-->
            <!--<div class="forget">忘记密码</div>-->

            <button class="singin" v-on:click="login">登录</button>
        </div>
    </div>



</div>
</template>
<script>
const common = require('commonFunc')

export default {
    name: 'login',
    data: function() {
        return {
            username: '',
            password: '',
            errorMessage: '',
            isA: true,
            tenant:common.getStoreData('tenant')
        }
    },
    mounted: function() {
        let _self = this
        common.setTitle('nca');
        document.onkeydown = function() {
            if (event.keyCode === 13) { //回撤
                _self.login()
            }
        }
        $('body').removeClass()
        $('body').addClass('pace-top')
        App.init()
    },
    methods: {
        login: function(event) {
            let _self = this;
            let encInfo = common.aesEncryptModeCFB((this.username.replace(/^\s+|\s+$/g,"")), this.password)
            this.$http.post('/api/auth', {
                username: this.username,
                identifyCode: encInfo[1],
                magicNo: encInfo[0],
                loginType: 'WEB'
            }).then((response) => {
                let token = response.headers.get('authorization')

                if (token) {
                    let userinfo = response.data.info
                    if (!userinfo.avatar) userinfo.avatar = '/static/images/base/head.jpg'
                    common.clearStoreData()
                    common.setStoreData('tenant',_self.tenant)
                    common.setStoreData('token', token)
                    common.setStoreData('userinfo', userinfo)
                    this.$router.push({
                        path: '/nca/homepage/NCAHomePageControl'
                    })
                } else {
                    this.errorMessage = '系统错误'
                    this.isA = false
                    common.clearStoreData()
                }
            }, (response) => {
                // error callback
                this.errorMessage = '用户名或者密码错误'
                this.isA = false
                console.log(response.status)
                console.log(response.data)
                common.clearStoreData()
            })
        },
      link: function () {
        window.open('http://www.miitbeian.gov.cn')
      }
    }
}
</script>
<style>
::-webkit-input-placeholder {
    /* WebKit browsers */
    color: #fff;
}

 :-moz-placeholder {
    /* Mozilla Firefox 4 to 18 */
    color: #fff;
}

 ::-moz-placeholder {
    /* Mozilla Firefox 19+ */
    color: #fff;
}

 :-ms-input-placeholder {
    /* Internet Explorer 10+ */
    color: #fff;
}


[v-cloak] {
    display: none;
}

.mainDiv {
    position: absolute;
    width: 100%;
    height: 100%;
    min-height: 768px;
    background:url("/static/images/logo/login_bg_crm.jpg") 50% 0 no-repeat;
    /*background-attachment: fixed;*/
    background-repeat: no-repeat;
    background-size: cover;
}

.title1 {
    font-size: 26px;
    color: white;
    position: absolute;
    top: 40px;
    left: 100px;
}

.title2 {
    position: absolute;
    top: 130px;
    right: 240px;
    width: 200px;
}

.title3 {
    position: absolute;
    top: 220px;
    right: 180px;
}

.title4 {
    width: 340px;
    position: absolute;
    top: 210px;
    left: 100px;
}

.title5 {
    position: absolute;
    top: 250px;
    left: 100px;
}

.title6 {
    position: absolute;
    top: 350px;
    left: 120px;
}

.title7 {
    position: absolute;
    bottom: 20px;
    left: 35%;
    color: #e5e5e5;
    font-size: 14px;
}

.link-margin {
    margin-left: -9px
}

.form {
    position: absolute;
    top: 240px;
    right: 180px;
    width: 320px;
    height: 320px;
    padding-left: 30px;
}

.userName {
    outline: none;
    border: 0px;
    background-color: rgba(255, 255, 255, 0.2);
    width: 260px;
    height: 40px;
    border-radius: 10px;
    margin-top: 50px;
    font-size: 14px;
    color: #fff;
    padding-left: 40px;
}

.userPng {
    position: absolute;
    left: 45px;
    top: 63px;
}

.password {
    outline: none;
    border: 0px;
    background-color: rgba(255, 255, 255, 0.2);
    width: 260px;
    height: 40px;
    border-radius: 10px;
    margin-top: 20px;
    font-size: 14px;
    color: #fff;
    padding-left: 40px;
}

.passPng {
    position: absolute;
    left: 45px;
    top: 122px;
}

.remember {
    position: absolute;
    top: 160px;
    left: 60px;
    color: #ffffff;
    cursor: pointer;
}

.forget {
    position: absolute;
    top: 160px;
    right: 35px;
    color: #ffffff;
    cursor: pointer;
}

.singin {
    border: none;
    outline: none;
    width: 260px;
    height: 40px;
    border-radius: 10px;
    margin-top: 20px;
    font-size: 14px;
    color: #fff;
    background: rgb(38, 208, 115);
}

#checkPng {
    position: absolute;
    top: 162px;
    left: 40px;
}

.errInfo {}


.tab-box {
    position: absolute;
    top: 440px;
    left: 100px;
}

.biaoti {
    text-align: center;
}

.erweima {
    width: 100px;
}

.imgwidth {
    width: 16px;
    margin-top: -3px;
    margin-right: 10px;
}

.wenzi {
    float: right;
}

.tab-wrapper {
    position: relative;
    height: 50px;
}

.tab-wrapper .tab-radio {
    display: none;
}

.tab-handler {
    position: relative;
    z-index: 2;
    display: block;
    float: left;
    height: 42px;
    padding: 0 41px;
    color: #bbb;
    font-size: 14px;
    line-height: 42px;
    cursor: pointer;
    transition: .3s;
    transform: scale(.9);
}

.tab-radio:checked+.tab-handler {
    color: #fff;
    background: url("/static/images/erweima/android-bg-1.png") no-repeat;
    transform: scale(1);
}

.tab-radio:checked+.tab-handler+.tab-content {
    visibility: visible;
    opacity: 1;
    transform: scale(1);
}

.tab-wrapper .tab-content {
    visibility: hidden;
    position: absolute;
    top: 42px;
    left: 0;
    width: 300px;
    padding: 30px 30px 14px 30px;
    color: #fff;
    font-size: 14px;
    line-height: 1.618em;
    border-radius: 0;
    /*background: url("img/ios-bg-2.png")no-repeat;*/
    opacity: 0;
    transition: transform .5s, opacity .7s;
    transform: translateY(20px);
}
</style>
