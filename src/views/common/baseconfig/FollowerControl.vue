<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">系统管理</a></li>
        <li class="active">关注审核列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">关注审核列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group" style="width:200px;">
                                <select class="form-control select2" multiple id="search_domain"></select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
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
const apiUrl = '/api/common/baseconfig/FollowerControl?method='

export default {
    data: function() {
        return {
            pagePara: '',
            workRow: {},
            oldRow: ''
        }
    },
    name: 'supplierControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')

        window.tableEvents = {
            'click .followerapprove': async function(e, value, row, index) {
                try {
                    await _self.$http.post(apiUrl + 'approve', {
                        functiondomain_id: row.functiondomain_id
                    });
                    $('#table').bootstrapTable("refresh")
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            'click .followerreject': async function(e, value, row, index) {
                try {
                    await _self.$http.post(apiUrl + 'reject', {
                        functiondomain_id: row.functiondomain_id
                    });
                    $('#table').bootstrapTable("refresh")
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            'click .followercancel': async function(e, value, row, index) {
                try {
                    await _self.$http.post(apiUrl + 'cancel', {
                        functiondomain_id: row.functiondomain_id
                    });
                    $('#table').bootstrapTable("refresh")
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }

        function actFormatter(value, row, index) {
            if (row.effect_state == 0) {
                return `
                      <div class="btn-group">
                          <a class="btn btn-info btn-xs m-r-5 followerapprove"><i class="fa fa-check"></i></a>
                          <a class="btn btn-info btn-xs m-r-5 followerreject"><i class="fa fa-times"></i></a>
                      </div>
                      `
            } else {
                return `
                    <div class="btn-group">
                        <a class="btn btn-info btn-xs m-r-5 followercancel">取消关注</a>
                    </div>
                    `
            }
        }

        function queryParams(params) {
            let domain_id = common.getSelect2Val('search_domain')
            if (domain_id) {
                params.domain_id = domain_id;
            }
            return JSON.stringify(params)
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
                    common.BTRowFormat('domain', '申请机构编号'),
                    common.BTRowFormatAlignLeft('api_name', '申请功能名称'),
                    common.BTRowFormatAlignLeft('domain_name', '申请机构名称'),
                    common.BTRowFormatAlignLeft('domain_contact', '联系人'),
                    common.BTRowFormat('domain_phone', '联系方式'),
                    common.BTRowFormat('follow_state', '申请状态'),
                    common.actFormatter('act', actFormatter, tableEvents)
                ],
                idField: 'functiondomain_id',
                uniqueId: 'functiondomain_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            })
            common.changeTableClass($table)
        }

        async function initPage() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info
            common.initSelect2($('#search_domain'), retData.domains)

            initTable()
            common.reSizeCall()
            console.log('init success')
        }

        initPage()
    },
    methods: {
        search: function(event) {
            $('#table').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
