<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">品质检验单列表</a></li>
            <li class="active">品质检验单</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">品质检验单</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="row" style="margin-top: 10px">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-2 control-label">品质质检单号</label>
                                <div class="col-sm-10">
                                    <input class="form-control" v-model="queryData.qualitycheck_id" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-2 control-label">采购单号</label>
                                <div class="col-sm-10">
                                    <input class="form-control" v-model="queryData.purchaseorder_id" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                        </div>

                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="searchOrderDC" placeholder="搜索物料名称、编码" id="search_textO" style="width: 230px">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="qualityCheckDetailTable"></table>
                        <div class="row">
                            <div class="btn-group col-sm-12" style="padding: 10px 0px">
                                <button class="btn btn-success pull-right" @click="printDetail"><i class="fa fa-print"></i> 打印</button>
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
const apiUrl = '/api/nca/purchasemanage/NCAQualityCheckControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            masterTable: null,
            searchOrderDC: null,
            queryData: {}
        }
    },
    name: 'NCAQualityCheckDetailControl',
    methods: {
        queryParamsOther: function (params) {
            params.qualitycheck_id = this.queryData.qualitycheck_id;
            params.search_text = this.searchOrderDC;
            return JSON.stringify(params);
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams);
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
                console.log('init success');
            }, (response) => {
                console.log('init error');
                common.dealErrorCommon(this, response)
            });
            this.initTable(0);
        },
        initTable:function(){
            let _self = this;

            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: this.queryParamsOther,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormat('materiel_unit', '单位'),
                    common.BTRowFormatAlignRight('qualified_number', '合格数量'),
                    common.BTRowFormatAlignRight('unqualified_number', '不合格数量'),
                    common.BTRowFormatWithFormatterAlignLeft('remark', '备注',common.remarkFormatter)
                ],
                idField: 'qualitycheckdetail_id',
                uniqueId: 'qualitycheckdetail_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {

                },
                onEditableSave: function(field, row, oldValue, $el) {

                }
            });
            common.changeTableClass(this.masterTable)
        },
        printDetail: async function() {
            let _self = this
            try {
                // let browser = common.BrowserType()
                // let filetype = 'image'
                // if (browser === 'Chrome' || browser === 'Safari') {
                //     filetype = 'pdf'
                // }
                let response = await _self.$http.post(apiUrl + 'QualityCheckDetailPrint', {
                    qualitycheck_id: _self.queryData.qualitycheck_id,
                    filetype: 'pdf'
                });
                let retData = response.data.info;
                printJS(retData.url)
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }

        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        this.masterTable = $('#qualityCheckDetailTable');
        this.initPage();
    }
}
</script>
<style scoped>
</style>
