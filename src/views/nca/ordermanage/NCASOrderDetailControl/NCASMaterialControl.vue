<!--大客户物料清单-->
<template>
<div>
    <div>
       <div class="panel-toolbar">
           <div class="form-inline" style="margin-left: 0px">
               <div class="form-group">
                   <div class="form-group">
                       <input class="form-control" id="search_text" placeholder="搜索物料编码、物料名称、规格型号" style="width: 230px;">
                   </div>
                   <div class="form-group">
                       <button id="search" class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                   </div>
                   <div class="form-group">
                       <button id="sendCreditLine" v-if="order.purchaser_type == 3"
                               class="btn btn-info" v-on:click="sendCreditLine"
                               v-bind:disabled="order.send_creditline_state==1">提交</button>
                   </div>
               </div>
               <div class="form-group  pull-right allMoney">
                   <!--<div>-->
                       <!--<span style="font-size: 14px">-->
                           <!--总金额:{{sumMoney}}-->
                       <!--</span>-->
                   <!--</div>-->
                   <span><strong>总金额:{{sumMoney}}</strong></span>
               </div>
           </div>
       </div>
        <div class="panel panel-inverse" style="margin-top: 10px">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                </div>
                <h4 class="panel-title">物料列表</h4>
            </div>
            <div class="panel-body auto-height">
                <table id="materielTable"></table>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCASOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            selectState: 2,
            sumMoney:0
        }
    },
    name: 'NCASMaterialControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {
            let _self = this;
        },
        order: function() {
            let _self = this;
            let $table = $('#materielTable');
            function queryParams(params) {
                params.order_id = _self.order.order_id;
                if ($('#search_text').val()) {
                    params.search_text = $('#search_text').val();
                }
                return JSON.stringify(params);
            }
            window.tableEvents = {
                'click .test_delete': function (e, value, row, index) {
                    if(_self.order.send_creditline_state==1){
                        common.dealPromptCommon('该订单物料已提交，不能删除');
                        return
                    }else {
                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'deleteOrdeRmateriel', $table, row, 'ordermateriel_id', function() {})
                    }
                }
            };
            function roomTypeFilter(value) {
                for (let i = 0; i < _self.order.roomsInfo.length; i++) {
                    if (_self.order.roomsInfo[i].room_id == value) {
                        return _self.order.roomsInfo[i].room_name
                    }
                }
                return ''
            }
            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 test_delete">删除</a>'
                ].join('')
            }
            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_material',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.sumMoney = res.info.sumMoney;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '类别', 'materilaCategory'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_procedure', '工序', 'materielProcedure'),
//                        common.BTRowFormatWithFormatter('room_id', '空间', roomTypeFilter),
                        common.BTRowFormatAlignRight('materiel_amount', '数量'),
                        common.BTRowFormatWithFormatterAlignRight('sale_price', '单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('sale_money', '金额',common.priceFormat),
                        common.BTRowFormatEdSelect2(_self, 'sap_order_state', 'SAP状态', 'sapState'),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    idField: 'ordermateriel_id',
                    uniqueId: 'ordermateriel_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        _self.$http.post(apiUrl + 'modify_material', {
                            'old': _self.oldRow,
                            'new': row
                        }).then((response) => {
                            $('#materielTable').bootstrapTable("refresh", queryParams)
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        })
                    },
                    onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
                })
                common.changeTableClass($table)
            }

            function initPage() {
                initTable();
            }
            $(function() {
                initPage()
            })
            $('#materielTable').bootstrapTable("refresh", queryParams)
        }
    },
    methods: {
        search: function() {
            let _self = this;
            $('#materielTable').bootstrapTable("refresh")
        },
        sendCreditLine:function(){
            let _self = this;
            let params={
                order_id:_self.order.order_id,
                purchaser_corporateclients_id:_self.order.purchaser_corporateclients_id
            };
            _self.$http.post(apiUrl + 'submitCreditLine', params).then((response) => {
                $('#materielTable').bootstrapTable("refresh");
                _self.$parent.refreshOrder();
                console.log('send_creditline_state',_self.order.send_creditline_state)
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        }
    }
}
</script>
<style scoped>
    .allMoney {
        font-size: 14px;
        line-height: 33px;
        position:relative;
    }
</style>
