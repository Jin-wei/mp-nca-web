<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">运营数据管理</a></li>
        <li class="active">附属公司列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">附属公司列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" id="search_text" placeholder="搜索机构编号" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
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
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCAAffiliatedCompanyControl?method=';

export default {
    data: function() {
        return {
            pagePara: '',
            workRow: {},
            oldRow: '',
            domain_id: ''
        }
    },
    name: 'NCAAffiliatedCompanyControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')

        function actFormatter(value, row) {
            return [
                '<a class="btn btn-primary btn-xs m-r-5 edit_detail btn-info-table">开启</a>',
                '<a class="btn btn-primary btn-xs m-r-5 appointment btn-info-table">关闭</a>',
            ].join('')
        }
        function queryParams(params) {
            params.search_text = $('#search_text').val();
            params.domain_id =_self.domain_id;
            return JSON.stringify(params)
        }

        window.tableEvents = {
            'click .edit_detail': (e, value, row, index) => {
                let _self = this
                let params={
                    domain_id:row.domain_id,
                    follow_domain_id:row.follow_domain_id
                }
                _self.$http.post(apiUrl + 'open', params).then((response) => {
                    $('#table').bootstrapTable("refresh")
                    return common.dealPromptCommon('已开启')
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            'click .appointment': (e, value, row, index) => {
                let _self = this
                let params={
                    domain_id:row.domain_id,
                    follow_domain_id:row.follow_domain_id
                }
                _self.$http.post(apiUrl + 'close', params).then((response) => {
                    $('#table').bootstrapTable("refresh")
                    return common.dealPromptCommon('已关闭')
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
        }

        function initTable() {
            $table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('domain_id', '组织机构代码'),
                    common.BTRowFormatAlignLeft('domain_name', '公司全称'),
                    common.BTRowFormatWithFormatterAlignLeft('domain_province', '公司地址',common.remarkFormatter),
                    common.BTRowFormatAlignLeft('domain_contact', '法人代表'),
                    // common.BTRowFormat('domain_phone', '联系方式'),
                    //common.actFormatter('act', actFormatter, tableEvents),
                ],
                idField: '',
                uniqueId: '',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
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
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                _self.domain_id = _self.pagePara.domain_list[0].id;
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        search: function(event) {
            $('#table').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
