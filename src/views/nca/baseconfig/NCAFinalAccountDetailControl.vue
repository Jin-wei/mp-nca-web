<template>
    <div>
        <!--&lt;!&ndash; begin breadcrumb &ndash;&gt;-->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">项目决算管理</a></li>
            <li class="active">项目决算详情</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">项目决算详情</h4>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="form-group col-sm-6">
                            <label class="control-label">项目编号: </label>
                            <span>{{projectId}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">项目名称: </label>
                            <span>{{projectDetail.project_name}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">地址: </label>
                            <span>{{projectDetail.province + ' ' + projectDetail.city + ' ' + projectDetail.district + ' ' + projectDetail.estate_name}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">状态: </label>
                            <span>{{projectDetail.project_state_text}}</span>
                        </div>

                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">项目决算详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="请输入空间" id="searchText"/>
                                </div>
                                <div class="form-group">
                                    <button  class="btn btn-info" v-on:click="searchClick">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group btn-info">
                                <button id="addM" class="btn btn-info" v-on:click="submitClick" v-if="projectDetail.project_state === '3' || projectDetail.project_state === '7'">
                                    提交
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectDetailTable"></table>
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
    const apiUrl = '/api/nca/baseconfig/NCAFinalAccountControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                projectId: '',
                projectDetail: {},
                oldRow:{},
                calculate_price:null,
                over_price:null,
                userinfo: common.getStoreData('userinfo')
            }
        },
        name: 'NCAProjectControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectDetailTable');
            _self.projectId = _self.$route.query.projectId


            function queryParams(params) {
                if ($('#searchText').val){
                    params.project_id = _self.projectId
                    params.search_text = $('#searchText').val()
                }
                return JSON.stringify(params);
            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 tableDetail">查看</a>'
                ].join('')
            }

            window.tableEvents = {
                'click .tableDetail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAFinalAccountSpaceControl',
                        query: {
                            projectId: _self.projectId,
                            projectDetailId: row.project_detail_id
                        }
                    })
                }
            };

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_detail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.calculate_price = res.info.total_price
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('roomtype_name', '户型名称'),
                        common.BTRowFormatAlignLeft('space_name', '空间名称'),
                        common.BTRowFormatWithFormatterAlignRight('space_final_amount', '空间决算金额',common.priceFormat),
                        common.BTRowFormatAlignRight('space_count', '空间数量'),
                        common.BTRowFormatWithFormatterAlignRight('space_total_amount', '预算总金额',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('space_final_total_amount', '决算总金额',common.priceFormat),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    sortOrder:'asc',
                    idField: 'project_detail_id',
                    uniqueId: 'project_detail_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($table)
            }

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info
                    let project = await _self.$http.post(apiUrl + 'search', {project_id: _self.projectId});
                    _self.projectDetail = project.data.info.rows[0];
                    for (let s of _self.pagePara.projectState) {
                        if (s.id === _self.projectDetail.project_state) {
                            _self.projectDetail.project_state_text = s.text;
                        }
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            async function initPage() {
                await getInit()
                initTable()
            }

            $(async function() {
                await initPage();
            });
        },
        methods: {
            submitClick: function () {
                let _self = this
                let param = {
                    project_id: _self.projectId,
                }
                _self.$http.post(apiUrl + 'submit_final_detail', param).then((response) => {
                    let retData = response.data
                    if (retData.errno === 0) {
                        _self.projectDetail.project_state = retData.info.project_state
                        for (let s of _self.pagePara.projectState) {
                            if (s.id === _self.projectDetail.project_state) {
                                _self.projectDetail.project_state_text = s.text;
                            }
                        }
                        common.dealSuccessCommon('提交成功')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })

            },
            searchClick: function() {
                let _self = this
                $('#projectDetailTable').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
