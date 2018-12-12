<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">出差交通接待报销申请</a></li>
            <li class="active">出差交通接待报销申请详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row hide-order-detail-desk">
            <div class="col-md-12">
                <!--<div class="panel panel-inverse">-->
                    <!--<div class="panel-body">-->
                        <!--<div>-->
                            <!--<ul class="timeline">-->
                                <!--<li class="li complete">-->
                                    <!--<div class="timestamp">-->
                                        <!--<span class="">{{trExpenseData.tr_expense_creator_name}} 新建申请</span>-->
                                    <!--</div>-->
                                    <!--<div class="status">-->
                                        <!--<h4>{{trExpenseData.created_at}}</h4>-->
                                    <!--</div>-->
                                <!--</li>-->
                                <!--<li class="li complete"  v-show="trExpenseData.tr_expense_state==='3'">-->
                                    <!--<div class="timestamp">-->
                                        <!--<span class="">{{trExpenseData.tr_expense_confirm_name}} 驳回</span>-->
                                    <!--</div>-->
                                    <!--<div class="status">-->
                                        <!--<h4>{{trExpenseData.tr_expense_confirm_time}}</h4>-->
                                    <!--</div>-->
                                <!--</li>-->
                                <!--<li class="li complete"  v-show="trExpenseData.tr_expense_state==='2'">-->
                                    <!--<div class="timestamp">-->
                                        <!--<span class="">{{trExpenseData.tr_expense_confirm_name}} 通过</span>-->
                                    <!--</div>-->
                                    <!--<div class="status">-->
                                        <!--<h4>{{trExpenseData.tr_expense_confirm_time}}</h4>-->
                                    <!--</div>-->
                                <!--</li>-->
                            <!--</ul>-->
                        <!--</div>-->
                        <!--&lt;!&ndash;<timeLine :order="order"></timeLine>&ndash;&gt;-->
                    <!--</div>-->
                <!--</div>-->

                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <span style="padding-right: 100px;">申请单编号：{{trExpenseData.tr_expense_code}}</span>
                                <span style="padding-right: 100px;">申请人：{{trExpenseData.tr_expense_creator_name}}</span>
                                <span style="padding-right: 100px;">申请单状态：{{trExpenseData.trapply_state_text }}</span>
                                <a style="margin-left: 230px;" class="btn btn-info btn-xs"  v-show="show_return == 1"  href="javascript:history.back(-1)">返回</a>
                                <button v-show="trExpenseData.tr_expense_state==='0' || trExpenseData.tr_expense_state==='3'" v-on:click="setTask" class="btn btn-info" style="margin:-6px 0 0 0">提交</button>
                            </div>
                            <h4 class="panel-title">出差交通接待报销申请详情</h4>
                        </div>
                        <div class="panel-body">

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <div class="input-group ">
                                        <span class="input-group-addon">申请预借费用(本位币)</span>
                                        <div>
                                            <input class="form-control" type="number" v-model="trExpenseData.tr_expense_pre_fee"
                                                   disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <div class="input-group ">
                                        <span class="input-group-addon">申请时间</span>
                                        <div class="form-inline">
                                            <div class="form-group">
                                                <input type="text" class="form-control" id="start_date"
                                                       placeholder="开始时间"  data-parsley-required="true" disabled="disabled" />
                                            </div>
                                            <div class="form-group">
                                                <span>-</span>
                                            </div>
                                            <div class="form-group">
                                                <input type="text" class="form-control" id="end_date"
                                                       placeholder="结束时间"  data-parsley-required="true" disabled="disabled" />
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div class="row" >
                                <div class="form-group col-sm-6">
                                    <div class="input-group ">
                                        <span class="input-group-addon">费用合计</span>
                                        <div>
                                            <input class="form-control" v-model="sumFee" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <div class="input-group ">
                                        <span class="input-group-addon">实际报销费用</span>
                                        <div>
                                            <input class="form-control" v-model="actualFee" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div>
                                <table  id="trTable"></table>
                            </div>
                        </div>
                    </div>


                    <div class="panel panel-inverse" style="background: #d9e0e7">
                        <div class="panel-body">

                            <div class="row pull-right">

                            </div>
                        </div>
                    </div>

            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCATransReceptionExpenseDetailControl?method='

export default {
    data: function() {
        return {
            initData:{},
            inputDisabled:false,
            trExpenseCode:common.getUrlParams("trExpenseCode"),
            userInfo: common.getStoreData('userinfo'),
            trState:'',
            trType:'',
            sumFee:0,
            actualFee:0,
            tableArr:[],
            trExpenseData: {
                tr_expense_code:'',//申请编号
                tr_expense_creator_name:'',//申请人名称
                tr_expense_confirm_time:'',//审核日期
                tr_expense_state:"",//审核状态
                tr_expense_start_time:'',//申请时间起始
                tr_expense_end_time:'',//申请时间截止
                tr_expense_pre_fee:'',//申请预借费用
                trapply_state_text:''//状态文字
            },
            show_return: 0
        }
    },
    name: 'transReceptionListControl',
    mounted: function() {
        let _self=this;
        _self.trTable = $('#trTable');
        _self.startDate = $('#start_date');
        _self.endDate = $('#end_date');
        _self.getInitData();

        common.initStartDatepicker(_self.startDate,_self.endDate);
        common.initEndDatepicker(_self.endDate,_self.startDate);

    },
    methods: {
        //获得静态下拉列表
        getInitData: async function () {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'init', {trExpenseCode: _self.trExpenseCode}).then((response) => {
                    if (response.body.info.expense_data && response.body.info.expense_data.length > 0) {
                        _self.trExpenseData = response.body.info.expense_data[0];
                    }
                    _self.trType = response.body.info.TR_EXPENSE_TYPE;
                    _self.trState = response.body.info.CHANGESTATE;
                    _self.formatState(_self.trState);
                    $('#start_date').val(_self.trExpenseData.tr_expense_start_time);
                    $('#end_date').val(_self.trExpenseData.tr_expense_end_time);
                    _self.show_return = _self.$route.query.show_return;
                    console.log('show_return',_self.show_return)
                    _self.initTable();
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        formatState:async function(status){
            let _self=this;
            for(let i in status){
                if(status[i].id===_self.trExpenseData.tr_expense_state){
                    _self.trExpenseData.trapply_state_text=status[i].text;
                }
            }
        },
        //申请审核
        setTask: async function () {
            let _self = this;
            var params = _self.trExpenseData;
            _self.$http.post(apiUrl + 'setTask', params).then((response) => {
                if(response.data.errno===0){
                    common.dealSuccessCommon('提交成功');
                    _self.getInitData();
                }else {
                    common.dealErrorCommon(_self, response)
                }

            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },
        queryParams: function (params) {
            let _self = this;
            params.trExpenseCode = _self.trExpenseCode;
            return JSON.stringify(params)
        },
        trTypeForamt: function (value, row) {
            let _self = this;
            for (let i of _self.trType) {
                if (value === i.id) {
                    return i.text
                }
            }
        },
        completeFee:function (rows) {
            let _self=this;
            _self.sumFee=0;
            _self.actualFee=0;
            if(!rows){
                rows=_self.trTable.bootstrapTable('getData');
            }
            for (let i in rows) {
                _self.sumFee = _self.sumFee + parseInt(rows[i].tr_detail_have_invoice_fee) + parseInt(rows[i].tr_detail_no_invoice_fee)
            }
            _self.actualFee = _self.sumFee  - parseInt(_self.trExpenseData.tr_expense_pre_fee);
        },
        dealErrorCommon: function(obj, response) {
            if (response.status > 699 && response.status < 800) {
                console.log('700 error')
                BootstrapDialog.show({
                    title: '<i class= "fa fa-fw fa-info-circle"></i><strong>错误信息</strong>',
                    cssClass: 'modal-danger',
                    message: '<i class="text-warning fa fa-fw fa-warning" style="font-size: 40px"></i>' + response.data.msg,
                    buttons: [{
                        label: '<i class= "fa fa-fw fa-close"></i>关闭',
                        cssClass: 'btn-outline',
                        action: function(dialogItself) {
                            dialogItself.close()
                        }
                    }]
                })
            } else if (response.status === 404) {
                obj.$router.push({
                    path: '/error404'
                })
            } else if (response.status === 401) {
                if (response.data.errno === -2) {
                    obj.setError('从其他地方登录', '从其他地方登录')
                } else {
                    obj.setError('未经授权：访问由于凭据无效被拒绝', '未经授权：访问由于凭据无效被拒绝')
                }

                obj.$router.push({
                    path: '/error401'
                })
            } else {
              obj.setError(response.status, response)
                obj.$router.push({
                    path: '/error'
                })
            }
        },
        rowModifyWithRefreshT: function (_self, act, row, key, tb) {
            _self.$http.post(act, {
                'old': _self.oldRow,
                'new': row
            }).then((response) => {

                let updaterow = response.data.info
                tb.bootstrapTable('updateByUniqueId', {
                    id: row[key],
                    row: updaterow
                })
                tb.bootstrapTable('resetView')

                window.setTimeout(function () {
                    let dataTable=_self.trTable.bootstrapTable('getData');
                    _self.completeFee(dataTable);
                },100)

                console.log('modify success')
            }, (response) => {
                _self.dealErrorCommon(this, response)
                console.log('modify error')
                tb.bootstrapTable("refresh")
            })
        },

        initTable: function () {
            let _self = this;
            window.filesEvents = {
                'change .fileupload': function (e, value, row, index) {
                    common.fileFileUploadRefresh(this, _self, row, apiUrl, 'uploadFile', $('#trTable'), 'tr_detail_id')

                },
                'click .delete-button': function (e, value, row, index) {
                    common.deleteFilesRRefresh(this, _self, row, apiUrl, 'removeFile', $('#trTable'), 'tr_detail_id');
                },
            }

            let columns;
            if (_self.trExpenseData.tr_expense_state === '1' || _self.trExpenseData.tr_expense_state === '2') {
                columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFormatterAlignLeft('tr_detail_fee_id', '费用名称', _self.trTypeForamt),
                    common.BTRowFormatWithFormatterAlignRight('tr_detail_expected_fee', '预计发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('tr_detail_have_invoice_fee', '有发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('tr_detail_no_invoice_fee', '无发票金额',common.priceFormat),
                    common.BTRowFormatWithFE('files', '附件', common.filesFormatterWithUpload),
                ]
            } else {
                columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFormatterAlignLeft('tr_detail_fee_id', '费用名称', _self.trTypeForamt),
                    common.BTRowFormatEdPrice('tr_detail_expected_fee', '预计发票金额',common.priceFormat),
                    common.BTRowFormatEdPrice('tr_detail_have_invoice_fee', '有发票金额',common.priceFormat),
                    common.BTRowFormatEdPrice('tr_detail_no_invoice_fee', '无发票金额',common.priceFormat),
                    common.BTRowFormatWithFE('files', '附件', common.filesFormatterWithUpload, filesEvents),
                ]
            }

            _self.trTable.bootstrapTable('destroy');
            _self.trTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    //计算合计金额
                    _self.completeFee(res.info.rows);
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columns,
                idField: 'tr_detail_id',
                uniqueId: 'tr_detail_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                showFooter: true,
                pageSize: common.pageSize(),
                undefinedText: '',
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function () {
                    $('[data-name="tr_detail_expected_fee"]').each(function() {
                        let actrow = _self.trTable.bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.tr_detail_fee_id != 1) {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true
                            })
                        }
                    })
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    _self.rowModifyWithRefreshT(this, apiUrl + 'modify', row, 'tr_detail_id', _self.trTable)
                }
            })
            common.changeTableClass(_self.trTable)
        }
    }
}
</script>
<style scoped>
    .timeline {
        list-style-type: none;
        display: flex;
        align-items: center;
        justify-content: center;
        background: white;
    }

    .timeline:before {
        background: white;
    }

    .li {
        transition: all 200ms ease-in;
    }

    .timestamp {
        margin-bottom: 20px;
        padding: 0px 40px;
        display: flex;
        flex-direction: column;
        align-items: center;
        font-weight: 100;
    }

    .status {
        padding: 0px 20px;
        display: flex;
        justify-content: center;
        border-top: 2px solid #D6DCE0;
        position: relative;
        transition: all 200ms ease-in;
    }

    .status h4 {
        font-weight: 400;
        font-size: 16px;
    }

    .status:before {
        content: "";
        width: 25px;
        height: 25px;
        background-color: white;
        border-radius: 25px;
        border: 1px solid #ddd;
        position: absolute;
        top: -15px;
        left: 42%;
        transition: all 200ms ease-in;
    }

    .li {
        margin-top: 0px;
    }

    .li.complete .status {
        border-top: 2px solid #66DC71;
    }

    .li.complete .status:before {
        background-color: #66DC71;
        border: none;
        transition: all 200ms ease-in;
    }

    .li.complete .status h4 {
        color: #66DC71;
    }
    /*#trTable,#trTable, .fixed-table-header{
        margin-left: 2px;
    }*/
</style>
