<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">系统管理</a></li>
        <li class="active">系统菜单列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">系统菜单列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline">
                        <div class="form-group pull-right">
                            <div class="form-group">
                                <button class="btn btn-primary btn-info" v-on:click="addF">增加目录
                              </button>
                                <button class="btn btn-primary btn-info" v-on:click="addM">增加菜单
                              </button>
                                <button class="btn btn-primary btn-info" v-on:click="editNode">编辑
                              </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body hidedesk" style="display:none;">
                    <ul id="tree" class="ztree"></ul>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="FModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">目录</h4>
                </div>
                <form class="form-horizontal" @submit.prevent="submitF" id="formF">
                    <div class="modal-body">
                        <div class="form-group">
                            <label class="col-md-2 control-label"><span class="table-required">*</span>目录名称</label>
                            <div class="col-md-9">
                                <input class="form-control" v-model="workRow.systemmenu_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存
                      </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <div class="modal fade" id="MModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">菜单</h4>
                </div>
                <form @submit.prevent="submitM" id="formM">
                    <div class="modal-body">
                        <div class="form-group">
                            <label><span class="table-required">*</span>功能名称</label>
                            <input class="form-control" v-model="workRow.systemmenu_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                        </div>
                        <div class="form-group">
                            <label><span class="table-required">*</span>功能路径</label>
                            <input class="form-control" v-model="workRow.api_path" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                        </div>
                        <div class="form-group">
                            <label>权限校验</label>
                            <select class="form-control select2" multiple style="width:100%" id="auth_flag"></select>
                        </div>
                        <div class="form-group">
                            <label>是否显示</label>
                            <select class="form-control select2" multiple style="width:100%" id="show_flag"></select>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/common/system/SystemApiControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            actNode: {},
            act: '' // 1新增 2修改
        }
    },
    name: 'MenuControl',
    mounted: function() {
        let _self = this;

        function iconDisplayFormatter(value, row) {
            return '<i class="fa fa-fw ' + row.iconSource + '"></i>'
        }

        async function initPage() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#auth_flag'), retData.authInfo)
                common.initSelect2($('#show_flag'), retData.tfInfo)
                _self.getData()
                $('#formF').parsley()
                $('#formM').parsley()
                common.reSizeCall();
                console.log('init success')
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }

        $(function() {
            initPage()
        });
    },
    methods: {
        getData: async function(event) {
            let _self = this
            try {
                let response = await _self.$http.post(apiUrl + 'search', {});
                let zNodes = response.data.info;
                let treeObj = $.fn.zTree.init($("#tree"), {}, zNodes);
                treeObj.expandAll(true);
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        addF: function(event) {
            let _self = this
            _self.workRow = {}
            _self.workRow.systemmenu_name = ''
            let nodeObj = $.fn.zTree.getZTreeObj("tree").getSelectedNodes();
            if (nodeObj && nodeObj.length > 0) {
                if (nodeObj[0].getPath().length > 4) return common.dealWarningCommon("系统最多只支持4级菜单");
                if (nodeObj[0].node_type === '01') return common.dealWarningCommon("菜单下不允许新增");
                _self.actNode = JSON.parse(JSON.stringify(nodeObj[0]));
            } else return common.dealWarningCommon("请选择一个节点");
            $('#formF').parsley().reset()
            _self.act = '1';
            $('#FModal').modal('show')
        },
        submitF: async function(event) {
            let _self = this
            try {
                if ($('#formF').parsley().isValid()) {
                    _self.workRow.parent_id = _self.actNode.systemmenu_id

                    if (_self.act === '1') {
                        let response = await _self.$http.post(apiUrl + 'addFolder', _self.workRow);
                    } else {
                        let response = await _self.$http.post(apiUrl + 'modifyFolder', _self.workRow);
                    }
                    $('#FModal').modal('hide');
                    _self.getData()
                }
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        addM: function(event) {
            let _self = this
            _self.workRow = {}
            _self.workRow.systemmenu_name = ''
            _self.api_path = ''
            $('#auth_flag').val(null).trigger('change')
            $('#show_flag').val(null).trigger('change')

            let nodeObj = $.fn.zTree.getZTreeObj("tree").getSelectedNodes();
            if (nodeObj && nodeObj.length > 0) {
                if (nodeObj[0].node_type === '01') return common.dealWarningCommon("菜单下不允许新增");
                _self.actNode = JSON.parse(JSON.stringify(nodeObj[0]));
            } else return common.dealWarningCommon("请选择一个节点");
            $('#formM').parsley().reset()
            _self.act = '1';
            $('#MModal').modal('show')
        },
        submitM: async function(event) {
            let _self = this
            try {
                if ($('#formM').parsley().isValid()) {
                    _self.workRow.parent_id = _self.actNode.systemmenu_id
                    _self.workRow.auth_flag = common.getSelect2Val('auth_flag')
                    _self.workRow.show_flag = common.getSelect2Val('show_flag')
                    if (_self.act === '1') {
                        let response = await _self.$http.post(apiUrl + 'addMenu', _self.workRow);
                    } else {
                        let response = await _self.$http.post(apiUrl + 'modifyMenu', _self.workRow);
                    }
                    $('#MModal').modal('hide');
                    _self.getData()
                }
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        editNode: async function(event) {
            let _self = this
            try {
                let nodeObj = $.fn.zTree.getZTreeObj("tree").getSelectedNodes();
                if (nodeObj && nodeObj.length > 0)
                    _self.actNode = JSON.parse(JSON.stringify(nodeObj[0]));
                else return common.dealWarningCommon("请选择一个节点");
                _self.act = '2';
                if (nodeObj[0].node_type === '00') {
                    _self.workRow = {}
                    _self.workRow.systemmenu_id = nodeObj[0].systemmenu_id
                    _self.workRow.systemmenu_name = nodeObj[0].systemmenu_name
                    $('#formF').parsley().reset()
                    $('#FModal').modal('show')
                } else if (nodeObj[0].node_type === '01') {
                    let response = await _self.$http.post(apiUrl + 'getApi', {
                        api_id: nodeObj[0].api_id
                    })
                    let retData = response.data.info
                    _self.workRow = {}
                    _self.workRow.systemmenu_id = nodeObj[0].systemmenu_id
                    _self.workRow.systemmenu_name = nodeObj[0].systemmenu_name
                    _self.workRow.api_path = retData.api_path
                    $('#auth_flag').val(retData.auth_flag).trigger('change')
                    $('#show_flag').val(retData.show_flag).trigger('change')

                    $('#formM').parsley().reset()
                    $('#MModal').modal('show')
                }
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
