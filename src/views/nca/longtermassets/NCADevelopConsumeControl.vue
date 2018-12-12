<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">研发资产管理</a></li>
            <li class="active">材料耗用单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料耗用单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索耗用单编号">
                                </div>
                            </div>
                            <!--<div class="form-group select-width">-->
                                <!--<select class="select2 form-control select-width" id="option_type">-->
                                    <!--<option value = "1">待摊资产项目</option>-->
                                    <!--<option value = "2">研发资产项目</option>-->
                                <!--</select>-->
                            <!--</div>-->
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="developconsumeTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCADevelopConsumeControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                state: 1,
            }
        },
        name: 'NCADevelopConsumeControl',
        mounted: function() {
            let _self = this;
            let $developconsumeTable = $('#developconsumeTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCADevelopConsumeDetailControl',
                        query: {
                            developconsume_id: row.developconsume_id
                        }
                    })
                }
            };

            function queryDevelopParams(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initDevelopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('consume_code', '耗用单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('develop_code', '项目编号'),
                    common.BTRowFormatAlignLeft('develop_name', '项目名称'),
                    common.BTRowFormatAlignLeft('consume_creator', '提交人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatAlignLeft('consume_examine', '审批人')
                ];
                $developconsumeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchDevelopConsume',
                    queryParams: queryDevelopParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'developconsume_id',
                    uniqueId: 'developconsume_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($developconsumeTable)
            }

            $(async function() {
                initDevelopTable();
                common.reSizeCall();
            })
        },
        methods: {
            queryConfirm: function(event) {
                // let _self = this;
                // _self.state = $('#option_type').val()
                $('#developconsumeTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
    .span1{
        font-size: 1.2em;
    }
    .padding-left{
        padding-left: 2em;
    }
    .select-width {
        width: 180px;
    }
</style>
