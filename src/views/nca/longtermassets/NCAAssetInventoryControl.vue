<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">资产盘点单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产盘点单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_AssetI" placeholder="搜索申请单编号、申请部门" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button class="btn btn-info" v-on:click="queryConfirm">
                                            <i class="fa fa-search"></i>
                                        </button>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addAPO" class="btn btn-info" v-on:click="addM" >增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="assetInventoryTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddAIModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增资产盘点单</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formAI">
                        <div class="modal-body">
                            <div class="form-horizontal" style="margin-top: 0px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>指派人</label>
                                    <div class="col-md-8">
                                        <input class="form-control" id="take_stock_confirm_id"  data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveRMAct">提交</button>
                        </div>
                    </form>
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
            pagePara:{},
            pagePara2:{},
            workRow:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            mulityFlag: '',
            take_stock_confirm_id: ''
        }
    },
    name: 'NCAAssetInventoryControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $assetInventoryTable = $('#assetInventoryTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            common.initSelect2($('#take_stock_status'), _self.pagePara.TAKES_STOCK_STATUS);

            initAssetPurchaseTable();
            common.reSizeCall();
            $('#formAI').parsley()
        }

        function nameFormatter2(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsNet(params) {
            params.search_text=$('#search_text_AssetI').val();
            return JSON.stringify(params)
        }
        function initAssetPurchaseTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetInventoryDetailControl',
                        query: {
                            take_stock_no: row.take_stock_no
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('take_stock_no', '申请单编号', nameFormatter2, tableEvents),
                common.BTRowFormatAlignLeft('user_name', '发布人'),
                common.BTRowFormat('release_time', '发布时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'take_stock_status', '状态', 'TAKES_STOCK_STATUS'),
                common.BTRowFormatAlignLeft('name', '指派人'),
                common.BTRowFormat('take_stock_confirm_time', '盘点时间')
            ];
            $assetInventoryTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParamsNet,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'take_stock_id',
                uniqueId: 'take_stock_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($assetInventoryTable)
        }
        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetInventoryTable').bootstrapTable("refresh")
        },
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#take_stock_confirm_id').val(null).trigger('change');
            $('#AddAIModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formAI').parsley().isValid()) {
                try {
                    _self.workRow.take_stock_confirm_id = _self.pagePara2.userId;

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    let take_stock_no = response.body.info
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetInventoryDetailControl',
                        query: {
                            take_stock_no: take_stock_no
                        }
                    })
                    $('#assetInventoryTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formAI').parsley().reset();
                    $('#AddAIModal').modal('hide');
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

                    $("#take_stock_confirm_id").val(_self.pagePara2.userName)
                    _self.take_stock_confirm_id = _self.pagePara2.userId
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
