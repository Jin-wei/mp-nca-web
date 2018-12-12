<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">资产维修管理</a></li>
            <li class="active">资产维修单详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <button class="btn btn-info btn-xs is-disabled" v-show="repair_state == 0 || repair_state == 1" v-on:click="saveAddAssetRepairMaterials">提交</button>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                            </div>
                            <span id="AssetsInfo"></span>
                            <span style="padding-left: 30px;" id="AssetsName"></span>
                            <span style="padding-left: 30px;" >状态：{{apply.repair_state_text}}</span>
                        </div>
                    </div>
                </div>
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产维修单详情</h4>
                    </div>
                    <div class="panel-toolbar pull-right">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <button class="btn btn-info" v-show="repair_state == 0 || repair_state == 1" v-on:click="addRM">增加维修材料</button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="assetRepairDetailTable"></table>
                    </div>

                    <div class="modal fade" id="AddAssetRepairMaterialsModal">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">增加维修材料</h4>
                                </div>
                                <form @submit.prevent="submitForm" id="formARM">
                                    <div class="modal-body">
                                        <div class="form-horizontal">
                                            <div class="form-group">
                                                <label class="col-md-3 control-label"><span class="table-required">*</span>维护材料名称</label>
                                                <div class="col-md-9">
                                                    <input class="form-control" v-model="workRow.repair_name" data-parsley-required="true" maxlength="40" data-parsley-maxlength="40">
                                                </div>
                                            </div>
                                            <div class="form-group">
                                                <label class="col-md-3 control-label"><span class="table-required">*</span>维护材料规格型号</label>
                                                <div class="col-md-9">
                                                    <input class="form-control" v-model="workRow.repair_model" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                                </div>
                                            </div>
                                            <div class="form-group">
                                                <label class="col-md-3 control-label"><span class="table-required">*</span>维护材料计量单位</label>
                                                <div class="col-md-9">
                                                    <input class="form-control" v-model="workRow.repair_unit" data-parsley-required="true" maxlength="7" data-parsley-maxlength="7">
                                                </div>
                                            </div>
                                            <div class="form-group">
                                                <label class="col-md-3 control-label"><span class="table-required">*</span>维护材料单价</label>
                                                <div class="col-md-9">
                                                    <input class="form-control" v-model="workRow.repair_price" data-parsley-required="true"  data-parsley-type="number">
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="submit" class="btn btn-info">保存</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>

                <!--资产故障诊断记录-->
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn" style="position:relative">
                            <button class="btn btn-info btn-xs is-disabled" v-show="repair_state == 0 || repair_state == 1" v-on:click="clickSeave">保存</button>
                            <a  href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产故障诊断记录</h4>
                    </div>
                    <div class="panel-body">
                        <div class="col-sm-12" style="margin-bottom: 20px;padding: 0px">
                                <textarea class="form-control is-disabled" v-model="apply2.fault_remark" id="fault_remark" placeholder="请输入故障诊断记录"  >
                                    </textarea>
                        </div>
                    </div>
                </div>
            </div>
        </div>


    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

    export default {
        data: function() {
            return {
                workRow: {},
                pagePara:{},
                fixedassetsrepair_id:'',
                apply:{},
                apply2:{},
                AssetsInfo: '',
                AssetsName: '',
                AssetsId: '',
                repair_state: '',
                show_return: 0
            }
        },
        name: 'NCAFixedAssetsRepairMaterials',

        mounted: function() {
            let _self = this;
            let $assetRepairDetailTable = $('#assetRepairDetailTable');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                _self.fixedassetsrepair_id = common.getUrlParams('fixedassetsrepair_id');
                _self.fixedassets_no = common.getUrlParams('fixedassets_no');
                _self.fixedassets_name = common.getUrlParams('fixedassets_name');
                response = await _self.$http.post(apiUrl + 'search_rdt', {fixedassetsrepair_id:_self.fixedassetsrepair_id});
                _self.apply = JSON.parse(JSON.stringify(response.data.info));
                let retData = response.data.info;
                _self.apply2 = _self.apply.fixedassetsrepair;
                _self.repair_state = _self.apply.fixedassetsrepair.repair_state;
                $('#repair_state').val(_self.repair_state).trigger('change');

                if (_self.fixedassets_no != null) {
                    let AssetsInfo = '固定资产维修单' + '&emsp;&emsp;&emsp;' + '固定资产编号：' + _self.fixedassets_no;
                    $("#AssetsInfo").html(AssetsInfo);
                } else {
                    let AssetsInfo = '固定资产维修单' + '&emsp;&emsp;&emsp;' + '固定资产编号：' + _self.apply.fixedassetsrepair.fixedassets_no;
                    $("#AssetsInfo").html(AssetsInfo);
                }
                if (_self.fixedassets_name != null) {
                    let AssetsName = '固定资产名称：' + _self.apply.fixedassetscheckdetail.fixedassets_name;
                    $("#AssetsName").html(AssetsName);
                } else {
                    let AssetsName = '固定资产名称：' + _self.apply.fixedassetscheckdetail.fixedassets_name;
                    $("#AssetsName").html(AssetsName);
                }

                if(_self.repair_state==0){
                    _self.apply.repair_state_text='待提交'
                }else if(_self.repair_state==1){
                    _self.apply.repair_state_text='未完成'
                }else if(_self.repair_state==2){
                    _self.apply.repair_state_text='已完成'
                }

                initAllDemandTable();
                common.reSizeCall();
                $('#formARM').parsley()
            }

            function queryParams(params) {
//                params.search_text=$('#search_text_APDC').val();
                params.fixedassetsrepair_id=common.getUrlParams('fixedassetsrepair_id');
                return JSON.stringify(params)
            }
            function operateFormatter(value, row, index) {
                if(_self.repair_state == 2){
                    return [
                        ''
                    ].join('')
                }else {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 repair_delete">删除</a>'
                    ].join('')
                }
            }

            function initAllDemandTable() {
                window.tableEvents = {
                    'click .repair_delete': function (e, value, row, index) {
                        console.log('_self.repair_state',_self.repair_state)
                        if(_self.repair_state==2){
                            return common.dealPromptCommon('该申请单已完成')
                        }
                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_rm', $assetRepairDetailTable, row, 'fixedassetsrepairmaterials_id', function() {})
                    }
                };

                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('repair_name', '维修材料名称'),
                    common.BTRowFormat('repair_model', '维修材料规格型号'),
                    common.BTRowFormat('repair_unit', '维修材料计量单位'),
                    common.BTRowFormatWithFormatterAlignRight('repair_price', '维修材料单价',common.priceFormat),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ];

                $assetRepairDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_rm',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'fixedassetsrepairmaterials_id',
                    uniqueId: 'fixedassetsrepairmaterials_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify_r', row, 'fixedassetsrepairmaterials_id', $assetRepairDetailTable)
                    }
                });
                common.changeTableClass($assetRepairDetailTable)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            clickSeave: function() {
                let _self = this
                let param = {
                    fixedassetsrepair_id: _self.apply.fixedassetsrepair.fixedassetsrepair_id,
                    fault_remark:$('#fault_remark').val(),
                }
                _self.$http.post(apiUrl + 'add_rr', param).then((response)=>{
                    common.dealSuccessCommon('诊断记录已保存');
//                    window.history.back()
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
            },
            addRM: function(event) {
                let _self = this;
                _self.workRow = {};
//                $('#addAPD').show();
                $('#AddAssetRepairMaterialsModal').modal('show');
            },

            saveAddAssetRepairMaterials:function(event){
                let _self = this;

                let params={
                    fixedassetsrepair_id: _self.fixedassetsrepair_id,
                    fixedassets_no: _self.apply.fixedassetsrepair.fixedassets_no,
                    fixedassets_name: _self.apply.fixedassetscheckdetail.fixedassets_name,
                }
                _self.$http.post(apiUrl + 'submit_r', params).then((response) => {
                    let retData = response.data.info;
                    _self.repair_state = retData.repair_state;
                    $('#purch_state').val(_self.repair_state).trigger('change');
                    common.dealSuccessCommonRF('提交成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            },

            submitForm: async function(event) {
                let _self = this;
                if ($('#formARM').parsley().isValid()) {
                    let _self = this
                    try {
                        _self.workRow.fixedassetsrepair_id = _self.fixedassetsrepair_id

                        let response = await _self.$http.post(apiUrl + 'add_rm', _self.workRow)
                        let retData = response.data.info;
                        $('#assetRepairDetailTable').bootstrapTable('insertRow', {
                            index: 0,
                            row: retData
                        });
                        console.log('success');
                        $('#assetRepairDetailTable').bootstrapTable("refresh");
                        $('#formARM').parsley().reset();
                        $('#AddAssetRepairMaterialsModal').modal('hide')
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            }

        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
