<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">收货管理</a></li>
            <li class="active">收货单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收货单&emsp;&emsp;&emsp;采购单号：{{queryData.purchaseorder_id}}</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="search_text" placeholder="搜索物料名称、编码" style="width: 230px">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                            <div class="pull-right">
                                <button class="btn btn-info" v-on:click="submitCollects" v-bind:disabled="selectedRows.length<=0">提交</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="goodsDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/inventorymanage/NCACollectGoodsControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            masterTable: null,
            queryData: {},
            search_text: null,
            selectedRows: [],
            oldRow: {}
        }
    },
    name: 'NCACollectGoodsDetailControl',
    methods: {
        queryParams: function (params) {
            params.purchase_id = this.queryData.purchaseorder_id;
            params.search_text = this.search_text;
            return JSON.stringify(params);
        },
        search: function() {
            this.masterTable.bootstrapTable("refresh");
        },
        submitCollects: function() {
          for(let c of this.selectedRows) {
            if(c.purchase_number == c.collect_number) {
              return common.dealWarningCommon(c.materiel_name + '已完成收货');
            }
            if (!(c.will_collect_number) || Number(c.will_collect_number)<= 0){
              return common.dealWarningCommon(c.materiel_name + '的收货数量不能为0');
            }
          }
          let param = {
            purchaseId: this.queryData.purchaseorder_id,
            supplierId: this.queryData.supplier_id,
            collects: this.selectedRows
          }
            this.$http.post(apiUrl + 'submit_collects', param).then(response => {
              if(response.data.errno == 0) {
                common.dealSuccessCommon('收货已提交');
              }
              this.masterTable.bootstrapTable("refresh");
            }, (response) => {
                common.dealErrorCommon(this, response);
                this.masterTable.bootstrapTable("refresh");
            });
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
            this.initTable();
        },
        initTable:function(){
            let _self = this;
            function calFormatter(value, row, index) {
                return row.purchase_number - row.collect_number;
            }
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_collects_detail',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                  {
                    checkbox: true
                  },
                  common.BTRowFormatWithIndex('NO.'),
                  common.BTRowFormat('materiel_code', '物料编码'),
                  common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                  common.BTRowFormat('materiel_format', '规格型号'),
                  common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                  common.BTRowFormatAlignRight('purchase_number', '订单数量'),
                  common.BTRowFormatWithFormatterAlignRight('remains_number', '待收货数量',calFormatter),
                  common.BTRowFormatEditableRight('will_collect_number', '收货数量', value => {
                    return value || 0;
                  })
                ],
                idField: 'qualitycheckdetail_id',
                uniqueId: 'qualitycheckdetail_id',
                striped: true,
                clickToSelect: false,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
              onEditableShown: function(field, row, $el, editable) {
                _self.oldRow = $.extend(true, {}, row)
              },
              onEditableSave: function(field, row, oldValue, $el) {
                let remain = row.purchase_number - row.collect_number;
                console.log(remain);
                let will = row.will_collect_number;
                console.log(will);
                let reg = /^[1-9]{1,}[\d]*$/;//正整数
                let validate = reg.test(will);
                if (!validate) {
                  common.dealWarningCommon('请输入正整数');
                  _self.masterTable.bootstrapTable("refresh");
                } else if (Number(will) > remain) {
                  common.dealWarningCommon('收货数量不能大于订单数量');
                  _self.masterTable.bootstrapTable("refresh");
                }
              },
              onCheckAll: function(rows) {
                for (let i = 0; i < rows.length; i++) {
                  _self.selectedRows.push(rows[i])
                }
              },
              onUncheckAll: function(rows) {
                _self.selectedRows = []
              },
              onCheck: function(row, $element) {
                _self.selectedRows.push(row)
                console.log(_self.selectedRows);
              },
              onUncheck: function(row, $element) {
                _self.selectedRows.splice($.inArray(row, _self.selectedRows), 1);
                console.log(_self.selectedRows);
              },
              onPageChange: function(number, size) {
                _self.selectedRows = []
              }
            });
            common.changeTableClass(this.masterTable)
        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        this.masterTable = $('#goodsDetailTable');
        this.initPage();
    }
}
</script>
<style scoped>
</style>
