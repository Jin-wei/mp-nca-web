<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">库存管理</a></li>
            <li class="active">其他入库申请单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">其他入库申请单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_apply" placeholder="搜索入库申请单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="apply_state"></select>
                                </div>
                                <div class="form-group">
                                    <button id="searchApply" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM1234" class="btn btn-info" v-on:click="addMateriel">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="applyTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <input class="form-control"  id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="getMateriel">
                        </div>
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <button type="button" class="btn btn-info" v-on:click="addPuchaseApplyDetail">提交</button>
                        </div>
                        <table id="tableSearch"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCAStcokInApplyControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                stockapply_id: ''
            }
        },
        name: 'NCAStockInApplyControl',
        mounted: function() {
            let _self = this;
            let $applyTable = $('#applyTable');
            let $tableSearch = $('#tableSearch');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2Placeholder($('#apply_state'), retData.purchaseApplyType, '状态');
                initPurchaseApplyTable();
                initTableMateriel();
                common.reSizeCall();
            }

            function purchaseApplyState(value, row) {
                for (let i = 0; i < _self.pagePara.purchaseApplyType.length; i++) {
                    if (_self.pagePara.purchaseApplyType[i].id == value) {
                        return _self.pagePara.purchaseApplyType[i].text
                    }
                }
                return ''
            }

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer" title="查看">',
                    value,
                    '</a>'
                ].join('')
            }
            function queryParamsApple(params) {
                params.search_text=$('#search_text_apply').val();
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }
                params.apply_state=common.getSelect2Val('apply_state');
                return JSON.stringify(params)
            }
            function initPurchaseApplyTable() {
                window.tableEvents = {
                    'click .show_detail': function(e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/inventorymanage/NCAStockInApplyDetailControl',
                            query: {
                                stockapply_id: row.stockapply_id
                            }
                        })
                    },
                    'click .delete_purchaseApply': function(e, value, row, index) {
                        if(row.apply_state==1){
                            return common.dealPromptCommon('该申请单待审批')
                        }else if (row.apply_state==3){
                            return common.dealPromptCommon('该申请单已通过')
                        }
                        common.rowDeleteWithApi(_self, '入库申请单删除', apiUrl + 'delete', $applyTable, row, 'stockapply_id', function() {})
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('stockapply_id', '入库申请单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('apply_submit', '申请人'),
                    common.BTRowFormatWithFormatter('apply_state', '申请单状态', purchaseApplyState),
                    common.BTRowFormatAlignLeft('apply_review', '审批人'),
                    common.BTRowFormat('apply_review_date', '审批时间'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatWithFormatterAlignLeft('apply_remark', '驳回说明',common.remarkFormatter),
                    // common.BTRowFormat('apply_materiel_remark', '备注'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 delete_purchaseApply">删除</a>';
                    }, tableEvents)
                ];
                $applyTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsApple,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'stockapply_id',
                    uniqueId: 'stockapply_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($applyTable)
            }

            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                return JSON.stringify(params)
            }
            function initTableMateriel() {
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getStockMateiel',
                    queryParams: queryParamsMateriel,
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
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEnumberWMinALginRight('apply_amount', '默认数量'),
                        common.BTRowFormatEditableLeft('stock_remarks', '备注')
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10,15,25,50,100],
                    locale: 'zh-CN',
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
                    }
                });
                common.changeTableClass($tableSearch)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                let _self = this;
                $('#applyTable').bootstrapTable("refresh")

            },
            addMateriel:async function () {
                let _self = this;
                $('#AddMaterielModal').modal('show');
                _self.searchedRow = [];
                $("#matNameOrCodeOrFormat").val('');
                $('#tableSearch').bootstrapTable("refresh");
                $("input[type=checkbox]").each(function() {
                    $(this).prop("checked", false);
                })
            },
            addPuchaseApplyDetail:function(event){
                let _self = this;
                let params=[];

                if(_self.searchedRow.length==0){
                    return common.dealPromptCommon('请选择物料')
                }
                for(let a of _self.searchedRow){
                    if(!a.apply_amount && a.apply_amount == 0){
                        return common.dealPromptCommon('请填写默认数量')
                    }
                }
                common.dealConfrimCommon('确认新增入库申请单？', async () => {
                    _self.$http.post(apiUrl + 'add', {apply_review:''}).then(response => {
                        _self.stockapply_id = response.body.info.stockapply_id;
                        if (_self.stockapply_id) {
                            console.log('_self.stockapply_id',_self.stockapply_id)
                            for(let s of _self.searchedRow){
                                if(s.apply_amount && s.apply_amount!=0){
                                    params.push({
                                        stockapply_id:_self.stockapply_id,
                                        materiel_id:s.materiel_id,
                                        apply_amount:s.apply_amount,
                                        stock_remarks:s.stock_remarks
                                    })
                                } else {
                                    return common.dealPromptCommon('请填写默认数量')
                                }
                            }
                            _self.$http.post(apiUrl + 'addStockApplyDetail', params).then((response) => {
                                let retData = response.data.info;
                                $('#stockDetailTable').bootstrapTable("refresh");
                                _self.searchedRow=[];
                                $('#AddMaterielModal').modal('hide');
                                // common.dealSuccessCommon('保存成功');
                            }, (response) => {
                                common.dealErrorCommon(_self, response)
                            });

                            _self.$http.post(apiUrl + 'setTask', { stockapply_id : _self.stockapply_id }).then((response) => {
                                let retData = response.data.info;
                                $('#tableSearch').bootstrapTable("refresh");
                                _self.searchedRow=[];
                                common.dealSuccessCommon('提交成功');
                                // window.history.back()
                            }, (response) => {
                                common.dealErrorCommon(_self, response)
                            });

                        }
                        $('#applyTable').bootstrapTable("refresh");
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                });
            },
            getMateriel:function(event){
                let _self = this;
                $('#tableSearch').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>