<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">入库</a></li>
            <li class="active">入库详情</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">入库详情</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索物料编码、物料名称" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addO">提交</button>
                            </div>
                        </div>
                    </div>

                    <div class="panel-body auto-height hidedesk">
                        <table id="qualityMaterielTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCABuyInControl?method=';

    export default {
        data: function() {
            return {
                masterTable: null,
                pagePara: {},
                materielData: {},
                buyOrderData: {},
                zoneArray: [],
                searchText: null,
                materiels: [],
                materiel: [],
                qualitycheck_id: '',
                warehouse_id: '',
                warehouse_zone_id: ''
            }
        },
        name: 'NCABuyInMaterielControl',
        methods: {
            queryParams: function (params) {
                params.qualitycheck_id = this.qualitycheck_id;
                params.warehouse_id = this.warehouse_id;
                params.warehouse_zone_id = this.warehouse_zone_id;
                params.materiels = this.materiels;
                params.search_keyword = this.searchText;
                params.materiel_id = this.materielData.materiel_id;
                return JSON.stringify(params);
            },
//        selectFormat: function (rowid, rowname, dataList, width = 200) {
//            return {
//                field: rowid,
//                title: rowname,
//                class: 'text-nowrap',
//                align: 'center',
//                valign: 'middle',
//                editable: {
//                    type: 'select2',
//                    emptytext: '未选择',
//                    select2: {
//                        data: dataList,
//                        width: width
//                    },
//                    display: function(value) {
//                        let showText = '';
//                        $(dataList).each(function() {
//                            if (value && value !== 'undefined') {
//                                if (this.id == value) {
//                                    showText = this.text;
//                                    return false
//                                }
//                            }
//                        });
//                        $(this).html(showText);
//                    }
//                }
//            }
//        },
            search: async function() {
                $('#qualityMaterielTable').bootstrapTable("refresh");
            },
            initTable: function () {
                this.masterTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getQualityCheckListForMateriel',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格'),
                        common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'materielInfo'),
//                    this.selectFormat('warehouse_zone', '仓区', this.zoneArray),
                        common.BTRowFormatEdSelectAlignLeft(this, 'warehouse_zone_id', '仓区', 'wareHouseZoneInfo'),
                        common.BTRowFormatAlignRight('qualified_number', '需求数量'),
                        common.BTRowFormatAlignRight('finishStock_number', '已入库数量'),
                        common.BTRowFormatEditableRight('stock_operate_amount', '入库数量', value => {
                            return value || 0;
                        })
                    ],
                    sortOrder:'asc',
                    idField: 'qualitycheckdetail_id',
                    uniqueId: 'qualitycheckdetail_id',
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
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
//                        common.rowModifyWithT(this, apiUrl + 'modifyQualityDetail', row, 'qualitycheckdetail_id', this.masterTable)

                        this.$http.post(apiUrl + 'modifyQualityDetail',{new:row}).then((response) => {
                        }, (response) => {
                            this.masterTable.bootstrapTable('updateByUniqueId', {
                                id: row.qualitycheckdetail_id,
                                row: this.oldRow
                            });
                            common.dealErrorCommon(this, response)
                        });
                    }
                });
                common.changeTableClass(this.masterTable)
            },
            initPage: function () {
                this.$http.post(apiUrl + 'initDetail', {
                    warehouse_id:this.warehouse_id,
                    warehouse_zone_id: this.warehouse_zone_id
                }).then(response => {
                    this.pagePara = response.data.info;
//                this.buyOrderData.warehouse_id = this.pagePara.wareHouseInfo.warehouse_id;
//                this.zoneArray = this.pagePara.wareHouseZoneInfo;
                    for (let i = 0; i < this.pagePara.materielInfo.length; i++) {
                        let item = this.pagePara.materielInfo[i];
                        if (item.id == this.materielData.materiel_unit) {
                            this.materielData.materiel_unit_name = item.text;
                            break;
                        }
                    }
                    this.initTable();
                }, (response) => {
                    console.log('init error');
                    common.dealErrorCommon(this, response)
                });
            },
            refreshMateriel: function() {
                this.masterTable.bootstrapTable("refresh", this.queryParams);
            },
            addO: function () {
                let params={
                    qualitycheckdetail_id: this.qualitycheckdetail_id,
                    qualitycheck_id: this.qualitycheck_id,
                    quality_bill_code: String(new Date().getTime()),
                    warehouse_id: this.warehouse_id,
                    materiels:this.materiels
                }

                this.$http.post(apiUrl + 'addStockMapDetailFromQuality', params).then((response) => {
                    let retData = response.data.info;
                    $('#qualityMaterielTable').bootstrapTable("refresh");
                    common.dealSuccessCommon('保存成功');
//                this.materiels = []
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });
            }
        },
        mounted: function() {
            let queryData = this.$route.query;
            this.materiels = queryData.materiels;
            console.log(this.materiels);
            for (let i = 0; i < this.materiels.length; i++) {
                this.materiel.push(this.materiels[i].materiel_id)
            }
            this.qualitycheck_id = queryData.qualitycheck_id;
            this.warehouse_id = queryData.warehouse_id;
            this.warehouse_zone_id = queryData.warehouse_zone_id;
            this.masterTable = $('#qualityMaterielTable');
            this.initPage();
        }
    }
</script>
<style scoped>
</style>
