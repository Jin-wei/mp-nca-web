<template>
    <div>
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">销售出库管理</a></li>
            <li class="active">出库单</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <span class="panel-title">出库单&emsp;&emsp;&emsp;销售单号&nbsp;:&nbsp;&nbsp;&nbsp;{{workRow.order_id}}</span>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索物料编码、物料名称" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">
                                    出库
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="modal fade" id="stockOut">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">出库信息</h4>
                                </div>
                                <form @submit.prevent="addOutAct" id="formA">
                                    <div class="modal-body">
                                        <div class="row">
                                            <div class="form-group col-sm-6">
                                                <label class="col-sm-5 control-label"><span class="table-required">*</span>请选择仓库</label>
                                                <div class="col-sm-7">
                                                    <select class="form-control select2" id="warehouse_id" data-parsley-required="true"></select>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-6">
                                                <label class="col-sm-5 control-label"><span class="table-required">*</span>请选择仓区</label>
                                                <div class="col-sm-7">
                                                    <select class="form-control select2" id="warehouse_zone_id" data-parsley-required="true"></select>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="submit" class="btn btn-primary btn-info">提交</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCASaleOutControl?method=';

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                searchedRow:[],
                oldRow: ''
            }
        },
        name: 'NCASaleOutDetailControl',
        mounted: function() {
            let _self = this;
            let $table = $('#table');
            let order_id = _self.$route.query.order_id;

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSaleOutOrderMateriel',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        {
                            field: 'state',
                            checkbox: true
                        },
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('demand_amount', '需要出库总数量'),
                        common.BTRowFormatAlignRight('done_count', '已出库数量')
                        //common.BTRowFormatEditable('waitoutapply_amount', '本次出库总数量')
                    ],
                    idField: 'alldemand_id',
                    uniqueId: 'alldemand_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
//                    onEditableSave: function(field, row, oldValue, $el) {
//                        common.rowModifyWithT(_self, apiUrl + 'modifyWaitApply', row, 'stockoutapplydetail_id', $table)
//                    },
                    onCheckAll: function(rows) {
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchedRow.push(rows[i])
                        }
                    },
                    onUncheckAll: function(rows) {
                        _self.searchedRow = []
                    },
                    onCheck: function(row, $element) {
                        _self.searchedRow.push(row)
                    },
                    onUncheck: function(row, $element) {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    },
                    onPageChange: function(number, size) {
                        _self.searchedRow = []
                    }
                })
                common.changeTableClass($table)
            }

            function queryParams(params) {
                if($('#warehouse_id').val()){
                    params.warehouse_id = $('#warehouse_id').val()
                }
                params.search_text = $('#search_text').val();
                params.order_id = _self.$route.query.order_id;
                return JSON.stringify(params)
            }

            $('#warehouse_id').on('select2:select', async function(evt) {
                let params={
                    warehouse_id:common.getSelect2Val('warehouse_id')
                }
                let response = await _self.$http.post(apiUrl + 'getWarehouseZone', params);
                let retData = response.data.info;
                $("#warehouse_zone_id").html('');
                common.initSelect2Editable($('#warehouse_zone_id'), retData);
            });

            async function initData() {
                console.log('fasgagsfasfasas')
                _self.workRow = _self.$route.query;
                await _self.getPagePara()
                common.initSelect2($('#materiel_unit'), _self.pagePara.unitInfo);
                common.initSelect2($('#warehouse_id'), _self.pagePara.warehouseId);
                initTable();
                common.reSizeCall();
            }
            initData()
        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                try {
                    let response = await _self.$http.post(apiUrl + 'initSaleOut', {});
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            getWarehouseZone: async function() {
                let _self = this;
                let warehouseId = $('#warehouse_id').val();
                if (warehouseId) {
                    let response = await _self.$http.post(apiUrl + 'getWarehouseZone', {
                        warehouse_id: warehouseId
                    });
                    common.initSelect2($('#warehouse_zone_id'), _self.pagePara.warehouseZoneId);
                } else {
                    _self.$set(_self.pagePara, 'warehouseZoneId', []);
                }
            },
            search: function(event) {
                $('#table').bootstrapTable("refresh")
            },
            addM: function (event) {
                let _self = this;
                console.log(_self.searchedRow.length,'_self.chuku')
                if(_self.searchedRow.length === 0){
                    return common.dealPromptCommon("请选择需要出库的物料")
                }else{
                    $('#stockOut').modal('show');
                }
                $('#formA').parsley().reset();
                $('#warehouse_id').val(null).trigger('change');
                $('#warehouse_zone_id').val(null).trigger('change')
            },
            addOutAct: async function (event) {
                let _self = this;
                let formA = $('#formA');

                if (formA.parsley().isValid()) {
                    _self.workRow = {};
                    let params = {
                        order_id:_self.$route.query.order_id,
                        warehouse_id: common.getSelect2Val('warehouse_id'),
                        warehouse_zone_id:common.getSelect2Val('warehouse_zone_id'),
                        materiel:_self.searchedRow
                    };
                    formA.parsley().reset();
                    let url = 'NCASaleOutDetailControlOperate';
                    _self.$router.push({
                        path: url,
                        query: {
                            order_id:_self.$route.query.order_id,
                            warehouse_id: common.getSelect2Val('warehouse_id'),
                            warehouse_zone_id:common.getSelect2Val('warehouse_zone_id'),
                            materiel:_self.searchedRow
                        }
                    })

                    $('#stockOut').modal('hide');

                    _self.searchedRow = []

                }
            }
        }
    }
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:5px;
        right:0px;
    }
    .m_t{
        margin-top:-24px;
    }
    .p_a2{
        position:absolute;
        top:15px;
        right:242px;
    }
    .m_l{
        margin-left: 23px;
    }
</style>