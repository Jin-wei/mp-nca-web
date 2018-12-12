<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">运营管理</a></li>
            <li class="active">物料同步管理</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 p_r">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">物料同步</h4>
                    </div>
                    <div class="panel-body" style="display:none;">
                    </div>
                </div>
                <div class="b_w">
                    <button id="addSyn" class="btn btn-info p_a" v-on:click="addSyn">
                        同步
                    </button>
                    <span style="font-size:25px;">点击下方同步按钮，自动同步物料到造易系统</span>
                </div>

            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/baseconfig/NCAMaterielSyncControl?method='

    export default {
        data: function() {
            return {
                pagePara: '',
            }
        },
        name: 'NCAMaterielSyncControl',
        mounted: function() {
            let _self = this

            function initPage() {
                common.reSizeCall()
            }

            initPage()
        },
        methods: {
            addSyn: async function(event) {
                let _self = this;
                let response = await _self.$http.post(apiUrl + 'sync')
            },
        }
    }
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:290px;
        left:752px;
    }
    .b_w{
        background-color: white;
        width:70%;
        height:300px;
        border:3px solid #c0c0c0;
        margin:0 auto;
    }
</style>
