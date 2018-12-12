<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">运营数据管理</a></li>
        <li class="active">产品列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'产品列表'"></DomainChooseHeader>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索产品编码、名称" id="search_text" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="tableProduce"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增产品</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-11" style="padding-left: 0px">
                        <input class="form-control" id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-1" style="padding-left: 43px;padding-right: 0px">
                        <button type="button" class="btn btn-info" v-on:click="saveProduce">保存</button>
                    </div>
                    <table id="tableMateriel"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAProduceControl?method=';

import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            searchedRow:[],
            listTitle: ''
        }
    },
    name: 'NCAProduceControl',
    components: {
        DomainChooseHeader
    },
    methods: {
        getPagePara: async function(event) {
            let _self = this;
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData))
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },

        addM: async function() {
            let _self = this;
            await _self.initTableMateriel();
            $('#AddMaterielModal').modal('show');
            _self.searchedRow = [];
            $("input[type=checkbox]").each(function() {
                $(this).prop("checked", false);
            })
        },
        saveProduce: async function() {
            let _self = this;
            let params={
                searchedRow:_self.searchedRow
            };
            let response = await _self.$http.post(apiUrl + 'addProduce', params);
            $('#tableProduce').bootstrapTable("refresh")
            $('#AddMaterielModal').modal('hide');

        },

        queryConfirm: function(event) {
            $('#tableProduce').bootstrapTable("refresh")
        },
        searchMate: function(event) {
            let _self = this;
            _self.searchedRow = [];
            $('#tableMateriel').bootstrapTable("refresh")
        },

        queryParamsProduct:function(params){
            let domain_id = common.getSelect2Val('domain_select');
            if (domain_id) {
                params.domain_id = domain_id
            }
            params.search_text = $('#search_text').val();
            return JSON.stringify(params)
        },
        initTableProduce:async function(event){
            let _self = this;
            let $tableProduce = $('#tableProduce');
            window.tableEvents = {
                'click .search_produce': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAProduceDetailControl',
                        query: {
                            produceId:row.produce_id,
                            produceDomainId:row.prodoceDomainId
                        }
                    })
                },
                'click .delete_produce': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '产品删除', apiUrl + 'deleteProduce', $tableProduce, row, 'produce_id', function(){})
                }
            };
            $tableProduce.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProduce',
                queryParams: _self.queryParamsProduct,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '产品编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格编码'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价',common.priceFormat),
                    // common.BTRowFormat('materiel_award_cost', '发包价'),
                    common.actFormatter('act', ()=>{
                        return [
                            '<button class="btn btn-info btn-xs m-r-5 search_produce">查看</button>',
                            '<button class="btn btn-info btn-xs m-r-5 delete_produce">删除</button>'
                        ].join('')
                    }, tableEvents)
                ],
                idField: 'produce_id',
                uniqueId: 'produce_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $("[data-uniqueid]").each(function() {
                        let actrow = $('#tableProduce').bootstrapTable('getRowByUniqueId', this.getAttribute('data-uniqueid'));
                        if(actrow){
                            if (actrow.domain_id) {
//                                console.log(actrow.materiel_id + ':' + actrow.domain_id);
//                                console.log(_self.userinfo.domain_id)
                                if (actrow.domain_id != _self.userinfo.domain_id) {
                                    $(this).find('.delete_produce').each(function() {
                                        $(this).attr("disabled", true)
                                    })
                                }
                            }
                        }
                    })
                }
            });
            common.changeTableClass($tableProduce)
        },

        queryParamsMateriel:function(params){
            params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
            params.tableName='purduce';
            return JSON.stringify(params)
        },
        initTableMateriel:async function(){
            let _self = this;
            let $tableMateriel = $('#tableMateriel');
            $tableMateriel.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMateriel',
                queryParams: _self.queryParamsMateriel,
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
                    /*common.BTRowFormat('materiel_cost', '采购价'),
                    common.BTRowFormat('materiel_sale', '销售价'),*/
                    common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价',common.priceFormat),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatAlignLeft('domain_name', '所属机构')
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                clickToSelect: true,
                pagination: true,
                pageSize:common.pageSize(),
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
                },
                onPageChange: function(number, size) {
                    _self.searchedRow = []
                }
            })
            common.changeTableClass($tableMateriel)
        }

    },
    mounted: async function() {
        let _self = this;
        await _self.getPagePara();
        await _self.initTableProduce();
        common.reSizeCall();
    },
}
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:21px;
        right:10px;
    }
    .m_t{
        margin-top:-24px;
    }
</style>
