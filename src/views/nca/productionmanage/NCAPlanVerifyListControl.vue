<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">产品规划审核</a></li>
        <li class="active">产品规划审核</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <span>产品编码：{{materielInfo.materiel_code}}&emsp;产品名称：{{materielInfo.materiel_name}}</span>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">
                        产品规划审核
                    </h4>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="tableVerify"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAProductPlanControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            materielInfo: {},
            pagePara: {},
            queryData: {},
            searchedRow:[],
            searchText: null,
            tableVerify: null
        }
    },
    name: 'NCAPlanVerifyListControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'initPlanVerify', {});
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async getMaterielInfo() {
            try {
                const response = await this.$http.post(apiUrl + 'getMaterielByProductPlan', {product_id: this.queryData.productId});
                const result = response.data.info;
                if (result.count > 0) {
                    this.materielInfo = result.data[0];
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async initTablePlanVerify(){
            window.tableEvents = {
                'click .delete_produce': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProductPlan', this.tableVerify, row, 'product_id', function(){})
                }
            };
            this.tableVerify.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchPlanVerify',
                queryParams: (params) => {
                    params.require_type = 5;
                    params.product_id = this.queryData.productId;
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
                    common.BTRowFormat('require_name', '评审项目'),
                    common.BTRowFormatWithFE('', '评审内容', (value) => {
                        return `<a class="show_detail" style="cursor: pointer;">查看</a>`
                    }, {
                        'click .show_detail': (e, value, row, index) => {
                            this.$router.push({
                                path: '/nca/productionmanage/NCAPlanVerifyMaterielControl',
                                query: {
                                    productId: this.queryData.productId,
                                    materielId: this.queryData.materielId,
                                    materielCode: this.materielInfo.materiel_code,
                                    materielName: this.materielInfo.materiel_name,
                                    requireId: row.require_id,
                                    requireName: row.require_name
                                }
                            });
                        }
                    }),
                    common.BTRowFormatWithFormatterAlignLeft('require_description', '评审责任描述',common.remarkFormatter),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'require_user_id', '责任人', 'userInfo')
                ],
                idField: 'product_id',
                uniqueId: 'product_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row);
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modifyProductPlan', row, 'product_id', this.tableVerify);
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass(this.tableVerify)
        }
    },
    async mounted() {
        this.queryData = this.$route.query;
        this.tableVerify = $('#tableVerify');
        await this.getMaterielInfo();
        await this.getPagePara();
        await this.initTablePlanVerify();
        common.reSizeCall();
    },
}
</script>
<style scoped>
    .m_t{
        margin-top: 15px;
        margin-left: 10px;
        margin-right: 10px;
    }
</style>
