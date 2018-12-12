<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back();">供应商维护</a></li>
        <li class="active">供应商物料列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">供应商物料列表</h4>
                </div>
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
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="tableProduce"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCASupplierControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            searchedRow:[],
            masterTable: null
        }
    },
    name: 'NCASupplierProduceControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        queryConfirm(event) {
            $('#tableProduce').bootstrapTable("refresh")
        },
        searchMate(event) {
            this.searchedRow = [];
            $('#tableMateriel').bootstrapTable("refresh")
        },
        async initTableProduce(event){
            let $tableProduce = $('#tableProduce');
            window.tableEvents = {
                'click .search_produce': (e, value, row, index) => {
                    this.$router.push({
                        path: '/nca/baseconfig/NCAProduceDetailControl',
                        query: {
                            produceId:row.produce_id,
                            produceDomainId:row.prodoceDomainId
                        }
                    })
                },
                'click .delete_produce': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProduce', $tableProduce, row, 'produce_id', function(){})
                }
            };
            $tableProduce.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getBusinessOffer',
                queryParams: (params) => {
//                    params.domain_type = this.pagePara.domainTypeInfo[2].id;
                    params.search_text = $('#search_text').val();
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '产品编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格编码'),
//                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
//                    common.BTRowFormat('materiel_cost', '采购价'),
                    common.BTRowFormatWithFormatterAlignRight('PRICE', '销售价',common.priceFormat),
//                    common.BTRowFormat('materiel_award_cost', '发包价'),
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($tableProduce)
        },

        queryParamsMateriel(params){
            params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
            params.tableName='purduce';
            return JSON.stringify(params)
        }
    },
    async created() {
        this.masterTable = $('#tableProduce');
        await this.getPagePara();
        await this.initTableProduce();
        common.reSizeCall();
    }
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
