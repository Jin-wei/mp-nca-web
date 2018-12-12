<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">行政办公管理</a></li>
        <li class="active">出差交通接待报销申请列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">出差交通接待报销申请列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索申请单编号、申请人姓名" style="width: 230px;">
                        </div>
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="createdAt" placeholder="创建时间" />
                            </div>
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="transReceptionExpenseTable"></table>
                </div>
            </div>
        </div>
    </div>

</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCATransReceptionExpenseControl?method='

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
        _self.transReceptionTable = $('#transReceptionExpenseTable');
        _self.createdAt = $('#createdAt');
        _self.initData();
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
            params.tr_expense_creator_id=_self.userinfo.user_id;
            if(_self.isNotEmpty(_self.search_text)){
                params.search_text = _self.search_text;
            }
            if(_self.isNotEmpty(_self.applyDate)){
                params.created_at = _self.createdAt.val();
            }

            return JSON.stringify(params)
        },
        actFormatter: function (value, row) {
            if(row.tr_expense_state==='1' || row.tr_expense_state==='2'){
                return [
                    '<a class="btn btn-info btn-xs m-r-5 editDetail">查看</a>'
                ].join('')
            }else {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 editDetail">查看</a>'+
                    '<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>'
                ].join('')
            }

        },
        stateFormat:function (value,row) {
            if(value==='0'){
                return '待提交'
            }else if(value==='1'){
                return '已提交'
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
                        path: '/nca/baseconfig/NCATransReceptionExpenseDetailControl',
                        query: {
                            trExpenseCode: row.tr_expense_code
                        }
                    })
                },
                'click .tableDelete': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '交通接待申请删除', apiUrl + 'remove', _self.transReceptionTable, row, 'tr_expense_id')
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
                    common.BTRowFormat('tr_expense_code', '申请单编号'),
                    common.BTRowFormatAlignLeft('tr_expense_creator_name', '申请人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormat('tr_expense_confirm_time', '提交时间'),
                    common.BTRowFormatWithFormatter('tr_expense_state', '申请状态',_self.stateFormat),
                    common.BTRowFormatWithFormatterAlignLeft('tr_expense_rejected_description', '驳回原因',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'paymentconfirm_state', '付款状态', 'payment_confirm_state'),
                    common.BTRowFormatWithFE('', '操作', _self.actFormatter, tableEvents)
                    /*common.actFormatter('act', _self.actFormatter, tableEvents)*/
                ],
                idField: 'tr_expense_id',
                uniqueId: 'tr_expense_id',
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
        search: function(event) {
            let _self=this;
            _self.transReceptionTable.bootstrapTable("refresh", this.queryParams)
        },
        initData : function () {
            let _self=this;
            _self.$http.post(apiUrl + 'init', {}).then(response => {
                if(response.body.errno==0){
                    _self.pagePara = JSON.parse(JSON.stringify(response.body.info));
                    _self.initTable();
                }else{
                    console.log(response.msg);
                }
            }, (response) => {
                common.dealErrorCommon(this, response);
            });
        },
    }
}
</script>
<style scoped>
</style>
