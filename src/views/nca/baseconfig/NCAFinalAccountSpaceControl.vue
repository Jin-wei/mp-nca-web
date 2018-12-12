<template>
    <div>
        <!--&lt;!&ndash; begin breadcrumb &ndash;&gt;-->
        <!--<ol class="breadcrumb">-->
        <!--&lt;!&ndash;<li><a href="javascript:">事务管理</a></li>&ndash;&gt;-->
        <!--<li class="active">工程项目管理</li>-->
        <!--</ol>-->
        <div class="row" style="margin-top: 45px">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">项目信息</h4>
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
                        <h4 class="panel-title">空间决算详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <div class="form-group">
                                        <select class="select2 form-control select-width" id="select_space_position" @change="changePositionType" style="width: 150px">
                                            <option value="">请选择空间位置</option>
                                            <option v-for="type in pagePara.spacePositionInfo" v-bind:value="type.id">{{type.text}}</option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="worker_type" @change="changeWorkerType" style="width: 150px">
                                    <option value="">请选择工种类型</option>
                                    <option v-for="type in pagePara.professionInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                            <!--<div class="form-group pull-right" style="margin-right: 20px">-->
                                <!--<button id="saveM" class="btn btn-info" v-on:click="saveClick" v-if="projectDetail.project_state === '3' || projectDetail.project_state === '7'">-->
                                    <!--保存-->
                                <!--</button>-->
                            <!--</div>-->
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectSpaceDetailTable"></table>
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
                projectDetailId: '',
                projectDetail: {},
                oldRow:{},
                userinfo: common.getStoreData('userinfo')
            }
        },
        name: 'NCAFinalAccountSpaceControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectSpaceDetailTable');
            _self.projectId = _self.$route.query.projectId
            _self.projectDetailId = _self.$route.query.projectDetailId


            function queryParams(params) {
                params.project_detail_id = _self.projectDetailId
                if ($('#select_space_position').val()){
                    params.project_space_position = $('#select_space_position').val()
                }
                if ($('#worker_type').val){
                    params.worker_id = $('#worker_type').val()
                }
                return JSON.stringify(params);
            }

            function workerFormatter(value, row, index) {
                for (let w of _self.pagePara.professionInfo) {
                    if (w.id == value) {
                        return w.text;
                    }
                }
            }

            function operateFormatter(value, row, index) {
//                if (row.apply_state === '2') {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 tableDelete">查看</a>',
                    ].join('')
//                } else {
//                    return [
//                        '<a class="btn btn-primary btn-xs m-r-5" disabled>查看</a>',
//                    ].join('')
//                }

            }

            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/purchasemanage/NCAPurchaseApplyDetailControl',
                        query: {
                            purchaseapply_id: row.purchaseapply_id,
                            isShowSale: 'show'
                        }
                    })
                }
            };

            function initTable() {
                let columns = []
                if (_self.projectDetail.project_state === '3' || _self.projectDetail.project_state === '7') {
                    columns = [
                        common.BTRowFormat('project_space_id','施工项编号'),
                        common.BTRowFormatAlignLeft('project_space_position', '空间位置'),
                        common.BTRowFormatAlignLeft('project_space_name', '施工项'),
                        common.BTRowFormatWithFormatterAlignLeft('worker_id', '工种',workerFormatter),
                        common.BTRowFormatAlignRight('count', '预算数量'),
                        common.BTRowFormatEnumberWMin('actual_count', '实际数量'),
                        common.BTRowFormat('worker_unit', '计量单位'),
                        common.BTRowFormatWithFormatterAlignRight('worker_budget', '人工预算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_budget', '人工预算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_budget', '材料预算总价',common.priceFormat),
                        common.BTRowFormatEnumberWMin('worker_final_price', '人工决算单价'),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_final_price', '人工决算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_final_price', '材料决算总价',common.priceFormat),
                        common.BTRowFormatWithFE('', '采购申请单', operateFormatter, tableEvents)
                    ]
                } else {
                    columns = [
                        common.BTRowFormat('project_space_id','施工项编号'),
                        common.BTRowFormatAlignLeft('project_space_position', '空间位置'),
                        common.BTRowFormatAlignLeft('project_space_name', '施工项'),
                        common.BTRowFormatWithFormatter('worker_id', '工种',workerFormatter),
                        common.BTRowFormatAlignRight('count', '预算数量'),
                        common.BTRowFormatAlignRight('actual_count', '实际数量'),
                        common.BTRowFormat('worker_unit', '计量单位'),
                        common.BTRowFormatWithFormatterAlignRight('worker_budget', '人工预算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_budget', '人工预算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_budget', '材料预算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_final_price', '人工决算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_final_price', '人工决算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_final_price', '材料决算总价',common.priceFormat),
                        common.BTRowFormatWithFE('', '采购申请单', operateFormatter, tableEvents)
                    ]
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_space_detail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    sortOrder:'asc',
                    idField: 'project_space_id',
                    uniqueId: 'project_space_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_space', row, 'project_space_id', $table)
                    }
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
                common.initSelect2($('#space_worker_type'), _self.pagePara.professionInfo);
                $('#formProject').parsley()
            }

            $(async function() {
                await initPage();
            });
        },
        methods: {
            saveClick: function () {
                let _self = this
                _self.$http.post(apiUrl + 'save_final_space', {project_detail_id: _self.projectDetailId}).then((response) => {
                    let retData = response.data
                    if (retData.errno === 0) {
                        common.dealSuccessCommon('保存成功')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
            },
            changeWorkerType: function () {
                let _self = this
                $('#projectSpaceDetailTable').bootstrapTable("refresh", _self.queryParams)
            },
            changePositionType: function() {
                let _self = this
                $('#projectSpaceDetailTable').bootstrapTable("refresh", _self.queryParams)
            },

        }
    }
</script>
<style scoped>
</style>
