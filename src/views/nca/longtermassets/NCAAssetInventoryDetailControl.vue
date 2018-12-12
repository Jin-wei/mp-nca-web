<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">资产盘点管理</a></li>
        <li class="active">盘点单</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">申请单号</span>
                                <div>
                                    <input class="form-control" v-model="take_stock_no" readonly>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">盘点单</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_AID" placeholder="搜索盘点对象工号、姓名" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button class="btn btn-info" v-show="take_stock_status ==0" v-on:click="addM">增加</button>
                            <button class="btn btn-info" v-show="take_stock_status ==0" v-on:click="saveAddAssetID">提交</button>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <table id="assetIDTable"></table>
                </div>

                <div class="modal fade" id="AddAssetIDModal">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">新增盘点对象</h4>
                            </div>
                            <form @submit.prevent="submitForm" id="formAID">
                                <div class="modal-body">
                                    <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>盘点对象姓名</label>
                                            <div class="col-md-7">
                                                <input class="form-control" id="take_stock_people_id"  data-parsley-required="true" disabled>
                                            </div>
                                            <div class="form-group col-sm-2">
                                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="modal-footer">
                                    <button type="submit" class="btn btn-info" id="saveRMAct">保存</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCATakeStockSRV?method=';

import userSelectDialog from '../../../components/common/userSelectDialog.vue'
export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            take_stock_no:'',
            apply:{},
            take_stock_status: '',
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            mulityFlag: '',
            take_stock_people_id: '',
            show_return: 0
        }
    },
    name: 'NCAAssetInventoryDetailControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $assetIDTable = $('#assetIDTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#take_stock_status'), _self.pagePara.TAKES_STOCK_STATUS);
            _self.show_return = _self.$route.query.show_return;
            console.log('show_return',_self.show_return)

            _self.take_stock_no = common.getUrlParams('take_stock_no');
            response = await _self.$http.post(apiUrl + 'search_dt', {take_stock_no:_self.take_stock_no});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.take_stock_status = _self.apply.take_stock_status;
            $('#take_stock_status').val(_self.apply.take_stock_status).trigger('change');
            initAllDemandTable();
            common.reSizeCall();
            $('#formAID').parsley()
        }

        function queryParams(params) {
            params.search_text=$('#search_text_AID').val();
            params.take_stock_parent_no=common.getUrlParams('take_stock_no');
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetInventoryUsersDetailControl',
                        query: row
                    })

                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('user_id', '盘点对象工号'),
                common.BTRowFormatAlignLeft('name', '盘点对象姓名'),
                common.BTRowFormatAlignLeft('position_name', '岗位'),
                common.BTRowFormatAlignLeft('department_name', '部门'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $assetIDTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchDetail',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'take_stock_detail_id',
                uniqueId: 'take_stock_detail_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_pd', row, 'take_stock_detail_id', $assetIDTable)
                }
            });
            common.changeTableClass($assetIDTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetIDTable').bootstrapTable("refresh")
        },
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#take_stock_people_id').val(null).trigger('change');
            $('#addAPD').show();
            $('#AddAssetIDModal').modal('show');
        },
        saveAddAssetID:function(event){
            let _self = this;
            let tableData=$('#assetIDTable').bootstrapTable('getData');
            if(tableData.length===0){
                return  common.dealWarningCommon('请先增加数据！');
            }
            let params={
                take_stock_no: _self.take_stock_no
            }
            _self.$http.post(apiUrl + 'submit', params).then((response) => {
                let retData = response.data.info;
                _self.take_stock_status = retData.take_stock_status;

                $('#take_stock_status').val(_self.take_stock_status).trigger('change');
                common.dealSuccessCommon('提交成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },
        submitForm: async function(event) {
            let _self = this;
            if ($('#formAID').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.take_stock_parent_no = _self.take_stock_no,
                    _self.workRow.take_stock_people_id = _self.pagePara2.userId

                    let response = await _self.$http.post(apiUrl + 'addDetail', _self.workRow)
                    let retData = response.data.info;
                    $('#assetIDTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    });
                    console.log('success');
                    _self.workRow={};
                    $('#assetIDTable').bootstrapTable("refresh");
                    $('#formAID').parsley().reset();
                    $('#AddAssetIDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback: async function(users) {
            let _self = this;
            let params =  {
                user_id: _self.pagePara.userInfo.user_id,
                users: users
            }
            try {
                _self.$http.post(apiUrl + 'changeGroupAct', params).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));

                    $("#take_stock_people_id").val(_self.pagePara2.userName)
                    _self.take_stock_people_id = _self.pagePara2.userId
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
