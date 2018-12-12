<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">行政办公管理</a></li>
        <li class="active">出差交通接待申请单列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">出差交通接待申请单列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索申请单编号、申请人姓名" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="createdAt" placeholder="创建时间" />
                            </div>
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addApplay">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="transReceptionTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="editModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">审批</h4>
                </div>
                <form @submit.prevent="editSignDate" id="formB">
                    <div class="modal-body">
                        <div class="form-group">
                            <label>审批人</label>
                            <input type="text" class="form-control" id="sign_date2" v-model="rowData.domainsign_date" data-parsley-required="true"/>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">提交</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCATransReceptionListControl?method='

export default {
    data: function() {
        return {
            rowData: {},
            oldRow: {},
            search_text: null,
            masterTable: null,
            createdAt: null,
            userinfo: common.getStoreData('userinfo')
        }
    },
    name: 'transReceptionListControl',
    mounted: function() {
        let _self=this;
        _self.transReceptionTable = $('#transReceptionTable');
        _self.createdAt = $('#createdAt');
        _self.initTable();
        common.reSizeCall()
        /*_self.initConfirmPeople();*/
        common.initDatepicker(_self.createdAt);
    },
    methods: {
        isNotEmpty:function (data) {
            if(data!=='' && data!==null){
                return true
            }else {
                return false
            }
        },
        queryParams: function (params) {
            let _self=this;
            params.trapply_creator_id=_self.userinfo.user_id;
            if(_self.isNotEmpty(_self.search_text)){
                params.search_text = _self.search_text;
            }
            if(_self.isNotEmpty(_self.applyDate)){
                params.created_at = _self.createdAt.val();
            }

            return JSON.stringify(params)
        },
        actFormatter: function (value, row) {
            if(row.trapply_state==='1' || row.trapply_state==='2'){
                return [
                    '<a class="btn btn-info btn-xs m-r-5 editDetail">查  看</a>'
                ].join('')
            }else {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 editDetail">查  看</a>'+
                    '<a class="btn btn-xs btn-info tableDelete" style="margin-left: 5px">删  除</a>'
                ].join('')
            }

        },
        stateFormat:function (value,row) {
            if(value==='0'){
                return '待提交'
            }else if(value==='1'){
                return '审核中'
            }else if(value==='2'){
                return '已审核'
            }else if(value==='3'){
                return '已驳回'
            }else if(value===''){
                return ''
            }
        },
        initTable: function () {
            let _self=this;
            window.tableEvents = {
                'click .editDetail': (e, value, row, index) => {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCATransReceptionDetailControl',
                        query: {
                            trapplyId: row.trapply_id
                        }
                    })
                },
                'click .tableDelete': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '交通接待申请删除', apiUrl + 'remove', _self.transReceptionTable, row, 'trapply_id')
                }
            };
            _self.transReceptionTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('trapply_code', '申请单编号'),
                    common.BTRowFormatAlignLeft('trapply_creator_name', '申请人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormat('trapply_confirm_time', '审批时间'),
                    common.BTRowFormatWithFormatter('trapply_state', '申请状态',_self.stateFormat),
                    common.BTRowFormatWithFormatterAlignLeft('trapply_rejected_description', '驳回原因',common.remarkFormatter),
                    common.BTRowFormatWithFE('', '操作', _self.actFormatter, tableEvents)
                    /*common.actFormatter('act', _self.actFormatter, tableEvents)*/
                ],
                idField: 'user_id',
                uniqueId: 'user_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                undefinedText:'',
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modify', row, 'user_id', _self.masterTable)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass(_self.transReceptionTable)
        },

        addApplay: function () {
            let _self=this;
            common.dealConfrimCommon('确认新增交通接待申请？', async () => {
                _self.$http.post(apiUrl + 'add', {}).then(response => {
                if(response.body.errno==0){
                    _self.transReceptionTable.bootstrapTable("refresh", this.queryParams);
                }else{
                    console.log(response.msg);
                }
                }, (response) => {
                    common.dealErrorCommon(this, response);
                });
            });
        },
        search: function(event) {
            let _self=this;
            _self.transReceptionTable.bootstrapTable("refresh", this.queryParams)
        },

    }
}
</script>
<style scoped>
</style>
