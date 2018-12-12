<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">资产报废列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产报废列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control" id="search_text_FixedAOAR" placeholder="搜索固定资产编号、名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="search_documentf">
                                        <option value="">请选择部门</option>
                                        <option v-for="state in pagePara.departmentInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_DeferredAOAR" placeholder="搜索待摊资产项目编号、名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="search_documentd">
                                        <option value="">请选择部门</option>
                                        <option v-for="type in pagePara.departmentInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="searchAP" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==3">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_ValueCAR" placeholder="搜索低值易耗品编号、名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="search_documentv">
                                        <option value="">请选择部门</option>
                                        <option v-for="type in pagePara.departmentInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="searchApply" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right" v-show="tableType==1">
                                <button class="btn btn-info" v-on:click="addFixedAssetsOrderAR">生成固定资产报废单</button>
                            </div>
                            <div class="form-group pull-right" v-show="tableType==2">
                                <button id="addAPO" class="btn btn-info" v-on:click="addDeferredAssetsOrderAR">生成待摊资产报废单</button>
                            </div>
                            <div class="form-group pull-right" v-show="tableType==3">
                                <button id="addM1234" class="btn btn-info" v-on:click="addValueConsumablesAR">生成低值易耗品报废单</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#fixedAssetsDataAR" data-toggle="tab">
                                    <span>固定资产</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#deferredAssets" data-toggle="tab">
                                    <span>待摊资产</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#valueConsumables" data-toggle="tab">
                                    <span>低值易耗品</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fixedAssetsDataAR">
                                <table id="fixedAssetsDataARTable"></table>
                            </div>
                            <div class="tab-pane fade" id="deferredAssets">
                                <table id="deferredAssetsTable"></table>
                            </div>
                            <div class="tab-pane fade" id="valueConsumables">
                                <table id="valueConsumablesTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAAssetRetirementControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            workRow:{},
            tableType:1,
            index: '',
            fixedAssetsDataARTable: null,
            deferredAssetsTable: null,
            valueConsumablesTable: null,
            queryData: {},
            matNameOrCodeOrFormat: null,
            checkDetailFixed: [],
            checkDetailDeferred: [],
            checkDetailValue: []
        }
    },
    name: 'NCAAssetRetirementDetailControl',
    methods: {
        queryParamsFixed: function (params) {
            params.matNameOrCodeOrFormat = $('#search_text_FixedAOAR').val();
            params.department_id= $('#search_documentf').val();
            params.scrap_type = 1;
            return JSON.stringify(params);
        },
        queryParamsDeferred: function (params) {
            params.matNameOrCodeOrFormat = $('#search_text_DeferredAOAR').val();
            params.amortize_departmant_id= $('#search_documentd').val();
            params.scrap_type = 2;
            return JSON.stringify(params);
        },
        queryParamsValue: function (params) {
            params.matNameOrCodeOrFormat = $('#search_text_ValueCAR').val();
            params.department_id= $('#search_documentv').val();
            params.scrap_type = 3;
            return JSON.stringify(params);
        },
        fixedTable: function () {
            let _self = this;
            window.tableEvents = {
                'click .fixedSelect': function(e, value, row, index) {
                    if ($(this).prop('checked')) {
                        _self.checkDetailFixed.push(row);
                    } else {
                        _self.checkDetailFixed.splice($.inArray(row, _self.checkDetailFixed), 1);
                    }
                }
            };
            this.fixedAssetsDataARTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_a',
                queryParams: this.queryParamsFixed,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.actFormatter('checkAct', () => {return `<input type="checkbox" class="fixedSelect">`}, tableEvents),
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'fixedassets_category', '固定资产分类', 'fixedassetstype'),
                    common.BTRowFormatEditableRight('return_price', '可回收金额', value => {
                        return value || 0;
                    }),
                    common.BTRowFormatEditableRight('expend_price', '消耗费用', value => {
                        return value || 0;
                    })
                ],
                sortOrder:'asc',
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                }
            });
            common.changeTableClass(this.fixedAssetsDataARTable)
        },
        deferredTable: function () {
            let _self = this;
            window.tableEvents = {
                'click .deferredSelect': function(e, value, row, index) {
                    if ($(this).prop('checked')) {
                        _self.checkDetailDeferred.push(row);
                    } else {
                        _self.checkDetailDeferred.splice($.inArray(row, _self.checkDetailDeferred), 1);
                    }
                }
            };
            this.deferredAssetsTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_a',
                queryParams: this.queryParamsDeferred,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.actFormatter('checkAct', () => {return `<input type="checkbox" class="deferredSelect">`}, tableEvents),
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatEditableRight('return_price', '可回收金额', value => {
                        return value || 0;
                    }),
                    common.BTRowFormatEditableRight('expend_price', '消耗费用', value => {
                        return value || 0;
                    })
                ],
                sortOrder:'asc',
                idField: 'amortize_id',
                uniqueId: 'amortize_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                }
            });
            common.changeTableClass(this.deferredAssetsTable)
        },
        valueTable: function () {
            let _self = this;
            window.tableEvents = {
                'click .valueSelect': function(e, value, row, index) {
                    if ($(this).prop('checked')) {
                        _self.checkDetailValue.push(row);
                    } else {
                        _self.checkDetailValue.splice($.inArray(row, _self.checkDetailValue), 1);
                    }
                }
            };
            this.valueConsumablesTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_a',
                queryParams: this.queryParamsValue,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.actFormatter('checkAct', () => {return `<input type="checkbox" class="valueSelect">`}, tableEvents),
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormat('consumables_unit', '计量单位'),
                    common.BTRowFormatEditableRight('return_price', '可回收金额', value => {
                        return value || 0;
                    }),
                    common.BTRowFormatEditableRight('expend_price', '消耗费用', value => {
                        return value || 0;
                    })
                ],
                sortOrder:'asc',
                idField: 'consumables_detail_id',
                uniqueId: 'consumables_detail_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                }
            });
            common.changeTableClass(this.valueConsumablesTable)
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', { }).then(response => {
                this.pagePara = response.data.info;
                this.fixedTable();
                this.deferredTable();
                this.valueTable();
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        showTable:function(index){
            let _self = this;
            _self.tableType=index;
            if(index === 1 ){
                $('#fixedAssetsDataARTable').bootstrapTable("refresh")
            } else if (index === 2 ){
                $('#deferredAssetsTable').bootstrapTable("refresh")
            } else if (index === 3){
                $('#valueConsumablesTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#fixedAssetsDataARTable').bootstrapTable("refresh")
            } else if (_self.tableType === 2 ){
                $('#deferredAssetsTable').bootstrapTable("refresh")
            } else if (_self.tableType === 3){
                $('#valueConsumablesTable').bootstrapTable("refresh")
            }
        },
        addFixedAssetsOrderAR:async function () {
            let _self = this;
            let tableData=this.checkDetailFixed;
            console.log('checkDetailFixed',this.checkDetailFixed)
            if(tableData.length===0){
                return  common.dealWarningCommon('请选择要报废的固定资产！');
            }
            let params={
                checkDetailFixed:this.checkDetailFixed,
                scrap_type:1
            }
            this.$http.post(apiUrl + 'submit_d', params).then((response) => {
                let retData = response.data.info;
                $('#fixedAssetsDataARTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
                this.checkDetailFixed = []
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        addDeferredAssetsOrderAR: function(event) {
            let _self = this;
            let tableData=this.checkDetailDeferred;
            if(tableData.length===0){
                return  common.dealWarningCommon('请选择要报废的待摊资产！');
            }
            let params={
                checkDetailDeferred:this.checkDetailDeferred,
                scrap_type:2
            }

            this.$http.post(apiUrl + 'submit_d', params).then((response) => {
                let retData = response.data.info;
                $('#deferredAssetsTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
                this.checkDetailDeferred = []
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        addValueConsumablesAR:async function () {
            let _self = this;
            let tableData=this.checkDetailValue;
            if(tableData.length===0){
                return  common.dealWarningCommon('请选择要报废的低值资产！');
            }
            let params={
                checkDetailValue:this.checkDetailValue,
                scrap_type:3
            }

            this.$http.post(apiUrl + 'submit_d', params).then((response) => {
                let retData = response.data.info;
                $('#valueConsumablesTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
                this.checkDetailValue = []
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        this.fixedAssetsDataARTable = $('#fixedAssetsDataARTable');
        this.deferredAssetsTable = $('#deferredAssetsTable');
        this.valueConsumablesTable = $('#valueConsumablesTable');
        this.initPage();
    }
}
</script>
<style scoped>
</style>
