<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">行政办公管理</a></li>
            <li class="active">报销职级列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">报销职级列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" v-model="searchText" placeholder="搜索报销职级"
                                       style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增报销职级记录</h4>
                    </div>
                    <form @submit.prevent="addDm" id="formA">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>报销职级</label>
                                <div class="col-sm-6">
                                    <input class="form-control" min="1" step="1" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_name"
                                           maxlength="50" data-parsley-maxlength="50"  data-parsley-required="true">
                                </div>
                            </div>
                            <!--<div class="form-group col-sm-6">-->
                                <!--<label class="col-sm-6 control-label"><span class="table-required">*</span>接待住宿标准</label>-->
                                <!--<div class="col-sm-6">-->
                                    <!--<input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_reception_putup_level"-->
                                           <!--maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">-->
                                <!--</div>-->
                            <!--</div>-->
                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>出差住宿标准</label>
                                <div class="col-sm-6">
                                    <input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_trip_putup_level"
                                           maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>市内交通补助标准</label>
                                <div class="col-sm-6">
                                    <input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_downtown_traffic_level"
                                           maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>伙食补助标准</label>
                                <div class="col-sm-6">
                                    <input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_meal_level"
                                           maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>接待费标准</label>
                                <div class="col-sm-6">
                                    <input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_reception_level"
                                           maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">
                                </div>
                            </div>

                            <div class="form-group col-sm-6">
                                <label class="col-sm-6 control-label"><span class="table-required">*</span>油补标准</label>
                                <div class="col-sm-6">
                                    <input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_gas_level" maxlength="50"
                                           data-parsley-maxlength="50" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="row">
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-6 control-label"><span class="table-required">*</span>可使用交通金额</label>-->
                                    <!--<div class="col-sm-6">-->
                                        <!--<input class="form-control" type="number" onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="workRow.reimburserank_traffic_available"-->
                                               <!--maxlength="50" data-parsley-maxlength="50" data-parsley-required="true">-->
                                    <!--</div>-->
                                <!--</div>-->
                                <div class="form-group col-sm-6" style="padding-left: 19px">
                                    <label class="col-sm-6 control-label"><span class="table-required">*</span>可使用交通工具</label>
                                    <div class="col-sm-6">
                                        <select class="form-control select2" style="width:100%" id="reimburserank_traffic_tools" data-parsley-required="true" ></select>
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
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCAReimburseRankControl?method=';
    import Constant from '../../../assets/js/constant'

    export default {
        data: function () {
            return {
                pagePara: {},
                workRow: {},
                oldRow: '',
                suppliers: [],
                searchText: null,
                masterForm: null,
                masterTable: null,
                modal: null
            }
        },
        name: 'reimburseRankControl',
        mounted: function () {
            let _self = this;
            let rowDelete = function (_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function () {
                    _self.$http.post(apiUrl + 'remove', row).then((response) => {
                        $('#table').bootstrapTable('remove', {
                            field: key,
                            values: [row[key]]
                        });
                        common.dealSuccessCommon('删除成功');
                        console.log('delete success');
                    }, (response) => {
                        console.log('delete error');
                        common.dealErrorCommon(_self, response);
                    });
                });
            };

            window.tableEvents = {
                'click .tableDelete': function (e, value, row, index) {
                    rowDelete(_self, '删除', apiUrl, row, 'reimburserank_id');
                    _self.searchedRow = []
                }
            }
            this.masterForm = $('#formA');
            this.masterTable = $('#table');
            this.modal = $('#AddModal');
            this.initPage()
        },
        methods: {
            queryParams: function (params) {
                params.search_text = this.searchText;
                return JSON.stringify(params)
            },
            search: function (event) {
                this.masterTable.bootstrapTable("refresh", this.queryParams)
            },
            addM: function (event) {
                this.workRow = {}
                $('#reimburserank_traffic_tools').val(null).trigger('change');
                this.masterForm.parsley().reset()
                this.modal.modal('show')
            },
            addDm: function (event) {
                if (this.masterForm.parsley().isValid()) {
                    this.workRow.reimburserank_traffic_tools = common.getSelect2Val('reimburserank_traffic_tools');
                    this.$http.post(apiUrl + 'add', this.workRow).then((response) => {
                        let retData = response.data.info
                        this.masterTable.bootstrapTable('insertRow', {
                            index: 0,
                            row: retData
                        })
                        this.masterTable.bootstrapTable('resetView')
                        this.workRow = {};
                        common.dealSuccessCommon('增加成功');
                        this.modal.modal('hide');
                        console.log('add success');
                        this.masterTable.bootstrapTable("refresh", this.queryParams)
                    }, (response) => {
                        common.dealErrorCommon(this, response)
                    })
                }
            },

            initTable: function () {
                this.masterTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
//                        common.BTRowFormat('domain_name', '机构'),
                        common.BTRowFormatAlignLeft('reimburserank_name', '报销职级'),
                        // common.BTRowFormatEdNum('reimburserank_reception_putup_level', '接待住宿标准'),
                        common.BTRowFormatEdPrice('reimburserank_trip_putup_level', '出差住宿标准',common.priceFormat),
                        common.BTRowFormatEdPrice('reimburserank_downtown_traffic_level', '市内交通补助标准',common.priceFormat),
                        common.BTRowFormatEdPrice('reimburserank_meal_level', '伙食补助标准',common.priceFormat),
                        common.BTRowFormatEdPrice('reimburserank_reception_level', '接待费标准',common.priceFormat),
                        common.BTRowFormatEdPrice('reimburserank_gas_level', '油补标准',common.priceFormat),
                        // common.BTRowFormatEdNum('reimburserank_traffic_available', '可使用交通金额'),
                        common.BTRowFormatEdSelect2(this, 'reimburserank_traffic_tools', '可使用交通工具', 'trafficTools'),
                        common.BTRowFormatWithFE('', '操作', `<a class="btn btn-xs btn-info tableDelete">删除</a>`, tableEvents)
                       /* common.actFormatter('act', `<a class="btn btn-xs btn-info tableDelete">删除</a>`, tableEvents)*/
                    ],
                    idField: 'reimburserank_id',
                    uniqueId: 'reimburserank_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    undefinedText:'无',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modify', row, 'reimburserank_id', this.masterTable)
                    }
                })
                common.changeTableClass(this.masterTable)
            },
            initPage: async function () {
                common.reSizeCall();
                console.log('init success')
                let response = await this.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                this.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#reimburserank_traffic_tools'), retData.trafficTools);
                this.initTable();
                this.masterForm.parsley();
            }
        }
    }
</script>
<style scoped>
    .p_r {
        position: relative;
    }

    .p_a {
        position: absolute;
        top: 8px;
        right: 0px;
    }
</style>
