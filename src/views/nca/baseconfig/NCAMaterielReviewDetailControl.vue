<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">物料明细</a></li>
        <li class="active">任务详情</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a class="btn btn-info btn-xs" href="javascript:history.back(-1)">返回</a>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">物料明细</h4>
                </div>

                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="reviewDetailTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMaterielControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            review_materiel_code: ''
        }
    },
    name: 'NCAMaterielReviewDetailControl',
    mounted: async function() {
        let _self = this;
        let $table = $('#reviewDetailTable');
        _self.review_materiel_code = common.getUrlParams('review_materiel_code')

        function queryParams(params) {
            let domain_id = common.getSelect2Val('domain_select')
            if (domain_id) {
                params.domain_id = domain_id
            }
            params.review_materiel_code = common.getUrlParams('review_materiel_code')
            return JSON.stringify(params)
        }

        function initTable() {
            $table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('review_materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('review_materiel_name', '物料名称'),
                    common.BTRowFormat('review_materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_unit', '计量单位', 'unitInfo'),
                    // common.BTRowFormatAlignRight('review_materiel_cost', '采购预算价'),
                    // common.BTRowFormatAlignRight('review_materiel_sale', '销售指导价'),
                    // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_formula', '算料公式', 'formulaInfo'),
                    // common.BTRowFormat('review_materiel_formatcount', '算料规格'),
                    // common.BTRowFormat('review_materiel_formatunit', '规格单位'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_source', '来源分类', 'materielSource'),
                    // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_manage', '管理模式分类', 'materielManage'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_amto', '制品分类', 'materielAmto'),
                    // common.BTRowFormat('review_materiel_sale', '销售价'),
                    // common.BTRowFormat('review_materiel_tax', '税率'),
                    // common.BTRowFormat('review_materiel_loss', '损耗率'),
                    // common.BTRowFormatWithFormatterAlignLeft('review_materiel_describe', '描述',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_state_management', '状态分类', 'stateMmanagementn'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_procurement_type', '采购类型', 'procurementType'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'review_state', '状态', 'reviewInfo')
//                    common.BTRowFormatEdSelect2Disabled(_self, 'review_performer', '审核人', 'staffInfo')
                ],
                idField: 'review_materiel_id',
                uniqueId: 'review_materiel_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    common.DynamicEditableByDomain(_self, $('#reviewDetailTable'));
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_r', row, 'review_materiel_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            initTable();
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        getPagePara: async function(event) {
            let _self = this
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData))
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
