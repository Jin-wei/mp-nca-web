<template>
    <div>
        <div>
            <div class="panel-heading">
                <div class="row">
                    <div class="panel-heading-btn" v-if="!checkState('CHECKING')">
                        <button class="btn btn-info" v-on:click="startReviewClick()">
                            开始审核
                        </button>
                    </div>
                </div>

            </div>
            <div class="panel panel-inverse">
                <div class="panel-body">
                    <table id="internal-check-table"></table>
                </div>
            </div>
        </div>
        <div>
            <form @submit.prevent="calculateInterestRate" id="rateForm">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <div class="btn-group">
                                <button type="submit" v-if="!checkState('REVIEWING')" class="btn btn-info btn-xs">计算</button>
                            </div>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                        </div>
                        <h4 class="panel-title">报价信息</h4>
                    </div>
                    <div class="panel-body">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group">
                                    <span class="input-group-addon">物料总价￥</span>
                                    <input class="form-control" v-model="totalCost" disabled data-parsley-type="number" data-parsley-required="true" data-parsley-min="1">
                                </div>
                            </div>
                            <div class="form-group col-sm-3">
                                <div class="input-group">
                                    <span class="input-group-addon">发包价格￥</span>
                                    <input class="form-control" v-model="order.award_cost" disabled id="award_cost" data-parsley-type="number" data-parsley-required="true" data-parsley-min="0">
                                </div>
                            </div>
                            <!--<div class="form-group col-sm-3">-->
                                <!--<div class="input-group">-->
                                    <!--<span class="input-group-addon">其他费用￥</span>-->
                                    <!--<input class="form-control" v-model="order.other_cost" id="other_cost" data-parsley-type="number" data-parsley-required="true" data-parsley-min="0">-->
                                <!--</div>-->
                            <!--</div>-->
                            <div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">订单总价￥</span>
                                        <input class="form-control" v-model="orderTotalPrice" disabled data-parsley-type="number" data-parsley-required="true" data-parsley-min="1">
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-3">
                                <div class="input-group">
                                    <span class="input-group-addon">折扣%</span>
                                    <input class="form-control" v-model="discount" :disabled="checkState('REVIEWING')">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group">
                                    <span class="input-group-addon">最终报价￥</span>
                                    <input class="form-control" v-model="order.final_offer" disabled id="final_offer" data-parsley-type="number" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </form>
        </div>

        <div class="modal fade" id="fileModal1">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">导入文件</h4>
                    </div>

                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-md-12">
                                <label>选择文件：</label><span>（请选文件）</span>
                                <div>
                                    <input type="file" id="file" class="form-control input-sm " size="1">
                                    <input type="text" id="select1" hidden>
                                    <span class="margin-top-ten">{{filePath}}</span>
                                </div>
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
    const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method='


    export default {
        props: ['order', 'pagePara'],
        data: function() {
            return {
                userinfo: common.getStoreData('userinfo'),
                sendData: {},
                filePath:'',
                fileUrl: '',
                selectRow: {},
                totalCost: 0,//物料总价
                awardCost: 0,//发包费用
                otherCost: 0,//其他费用
                finalOffer: 0,//最终报价
                interestRate: '',//毛利率
                discount: null,//折扣
                orderTotalPrice: 0//订单总价
            }
        },
        name: 'internalCheckControl',
        mounted: function() {

        },
        watch: {
            pagePara: function() {
                let _self = this
            },
            order: function() {

                let _self = this
                let $table = $('#internal-check-table')


              //获取物料总价
              function getTotalCost () {
                let param = {
                  order_id: _self.order.order_id
                }
                _self.$http.post(apiUrl + 'get_totalCost', param).then((response) => {
                  _self.totalCost = response.data.info.totalCost;
                  _self.orderTotalPrice = _self.totalCost + _self.order.award_cost
                    if (_self.order.final_offer) {
                        _self.discount = _self.order.final_offer / _self.orderTotalPrice
                    }
                }, (error) => {
                  common.dealErrorCommon(_self, error)
                })
              }

              function initTable() {

                    window.tableEvents = {
                        'click .tableDelete': function(e, value, row, index) {
                            common.dealConfrimCommon('您确定删除么', function() {

                                _self.$http.post(apiUrl + 'delete_file', {internalReviewId:row.internal_review_id}).then((response) => {
                                    if (response.data.errno == 0) {
                                        _self.$parent.refreshOrder();
                                    } else {
                                        common.dealErrorCommon(_self, response)
                                    }
                                }, (response) => {
                                    common.dealErrorCommon(_self, response)
                                })
                            });
                        },
                        'click .selectFile': function(e, value, row, index) {
                            _self.selectRow = row;
                            $('#fileModal1').modal('show')
                        },
                        'click .openFile': function(e, value, row, index) {
                            window.open(row.file_url);
                        }
                    };

                    function userFormatter(value, row, index) {
                          for (let employee of _self.pagePara.employeeInfo) {
                              if (value == employee.id) {
                                  return employee.text;
                              }
                          }
                          return null;
                    }

                    window.designEvents = {
                        'change .fileupload': function(e, value, row, index) {
                            //上传之前先判断，执行人与当前用户是否相同，如果不同且有上传文件，则不允许直接上传，需要删除原来执行人上传的文件，然后在上传
                            if (_self.userinfo.user_id != row.duty_user_id) {
                                if (row.files.length > 0) {
                                    common.dealSuccessCommon('请删除当前上传文件之后，在重新上传');
                                    return;
                                }
                            }
                            common.fileFileUpload(this, _self, row, apiUrl, 'inter_review_update', $table, 'internal_review_id')
                        },
                        'click .delete-button': function(e, value, row, index) {
                            common.deleteFiles(this, _self, row, apiUrl, 'delete_file', $table, 'internal_review_id')
                        }
                    }

                    let columns;
                    if (_self.checkState('SIGNED')) {
                        columns = [
                            common.BTRowFormatWithIndex('NO.'),
                            common.BTRowFormatAlignLeft('require_name', '审核文档'),
                            common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                            common.BTRowFormatWithFormatterAlignLeft('duty_user_id', '责任人', userFormatter),
                            common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
                        ]
                    } else {
                        columns = [
                            common.BTRowFormatWithIndex('NO.'),
                            common.BTRowFormatAlignLeft('require_name', '审核文档'),
                            common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                            common.BTRowFormatEdSelectAlignLeft(_self, 'duty_user_id', '责任人', 'employeeInfo'),
                            common.BTRowFormatWithFE('files', '文件', common.filesFormatterWithUpload, designEvents)
                        ]
                    }

                    $table.bootstrapTable({
                        columns: columns,
                        idField: 'internal_review_id',
                        uniqueId: 'internal_review_id',
                        clickToSelect: true,
                        locale: 'zh-CN',
                        onEditableShown: function(field, row, $el, editable) {
//                        _self.oldRow = $.extend(true, {}, row)
                        },
                        onEditableSave: function(field, row, oldValue, $el) {
                            let param = {
                                internalReviewId: row.internal_review_id,
                                dutyUserId:row.duty_user_id,
                                orderId: _self.order.order_id
                            }

                            //判断当前责任时是否有上传的文件，如果有，则不允许更改责任人
                            if (row.files.length > 0) {
                                _self.$parent.refreshOrder();
                                common.dealSuccessCommon('请删除当前上传文件之后，在更改责任人');
                            } else {
                                _self.$http.post(apiUrl + 'set_duty', param).then((response) => {

                                }, (response) => {
                                    common.dealErrorCommon(_self, response)
                                })
                            }
                        },
                        onPostBody: function() {
                            $('[data-toggle="popover"]').each(function () {
                                $(this).popover()
                            })
                        }
                    });
                    common.changeTableClass($table);
                }

                function saveFile(fileName,fileUrl) {
                    _self.$http.post(apiUrl + 'save_file', {internalReviewId:_self.selectRow.internal_review_id,fileName:fileName,fileUrl:fileUrl,username:_self.userinfo.username}).then((response) => {
                        if (response.data.errno == 0) {
                            $('#fileModal1').modal('hide');
                            _self.$parent.refreshOrder();
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }

                $('#file').change(function() {
                    let files = this.files;
                    let formData = new FormData();
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();

                    _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno == 0) {
                            saveFile(response.data.info.name,response.data.info.url);
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })

                });

                function initPage() {
                    common.initSelect2($('#duty_user_id'), _self.pagePara.employeeInfo);
                    getTotalCost();
                    initTable();
                    $('#rateForm').parsley()
                }

                $(function() {
                    initPage();
                })


                _self.$http.post(apiUrl + 'search_internal_review', {orderId: _self.order.order_id}).then((response) => {
                    let retdata = response.data.info;
                    $table.bootstrapTable('load', {
                        data: retdata
                    })

                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                common.initImageViewer()
            }
        },
        methods: {
            startReviewClick:function () {
                let _self = this;
                _self.$http.post(apiUrl + 'start_internal_review', {orderId: _self.order.order_id,domainId:_self.userinfo.domain_id}).then((response) => {
                    _self.$parent.refreshOrder();
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            checkState: function(state, event) {

                let _self = this
                if (_self.order.statedict) {
                    if (state in _self.order.statedict) return true
                }
                return false
            },
          calculateInterestRate: function () {
            let _self = this
            let discount = _self.discount ? _self.discount : '1'

            if (parseFloat(discount) > 1) {
                common.dealWarningCommon('请输入小于等于1的折扣')
                return
            }
            if (parseFloat(discount) <= 0) {
              common.dealWarningCommon('请输入小于等于0的折扣')
              return
            }

            if ($('#rateForm').parsley().isValid()) {
              const awardCost = $('#award_cost').val()
              _self.order.final_offer = _self.orderTotalPrice * discount
              const param = {
                order_id: _self.order.order_id,
                interest_rate: 0,
                award_cost: parseFloat(awardCost),
                other_cost: 0,
                final_offer: parseFloat(_self.orderTotalPrice) * parseFloat(discount)
              }
              _self.$http.post(apiUrl + 'save_interestRate', param).then((response) => {
                _self.$parent.refreshOrder();
              }, (error) => {
                common.dealErrorCommon(_self, error)
              })
            }
          },
          priceCheck: function(event) {
            let _self = this
            _self.workRow.order_id = _self.order.order_id
            _self.workRow.final_offer = _self.order.final_offer
            if (_self.workRow.final_offer <= 0) {
              common.dealWarningCommon('请确认最终报价是否有误')
              return
            }
            _self.$http.post(apiUrl + 'priceCheck', _self.workRow).then((response) => {
              _self.$parent.refreshOrder()
            }, (response) => {
              common.dealErrorCommon(_self, response)
            })
          }
        }
    }
</script>
<style scoped>
</style>
