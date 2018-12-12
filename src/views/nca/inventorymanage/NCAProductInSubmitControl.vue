<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">入库</a></li>
            <li class="active">入库详情</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">入库详情</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索物料编码、物料名称" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="submitProductIn">提交</button>
                            </div>
                        </div>
                    </div>

                    <div class="panel-body auto-height">
                        <table id="submitMaterielTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCABuyInControl?method=';

  export default {
    data: function() {
      return {
        masterTable: null,
        queryData: '',
        pagePara: '',
        oldRow: '',
        searchText: null,
        warehouse_id: '',
        warehouse_zone_id: '',
        productivetask_id: '',
        materilas: []
      }
    },
    name: 'NCAProductInSubmitControl',
    methods: {
      search: async function() {
        $('#submitMaterielTable').bootstrapTable("refresh");
      },
      submitProductIn: function () {
        for (let m of this.materilas) {
          let remain = 0
          if (m.taskrelated_type) {
            remain = m.taskrelateddesign_number - m.related_stock_in_number
          } else {
            remain = m.taskdesign_number - m.stock_in_number;
          }
          let will = m.stock_operate_amount;
          let reg = /^[1-9]{1,}[\d]*$/;//正整数
          let validate = reg.test(will);
          if (!validate) {
            return common.dealWarningCommon('请输入正整数');
          } else if (Number(will) > remain) {
            return common.dealWarningCommon('入库数量不能大于待入库数量');
          }
        }
        let params={
          productivetask_id: this.productivetask_id,
          warehouse_id: this.warehouse_id,
          materiels: this.materilas,
          bill_code: String(new Date().getTime())
        }

        this.$http.post(apiUrl + 'submitProductInItems', params).then((response) => {
          let retData = response.data.info;
          common.dealSuccessCommon('提交成功');
        }, (response) => {
          common.dealErrorCommon(this, response)
        });
      }
    },
    mounted: function() {
      let _self = this
      let queryData = _self.$route.query
      _self.warehouse_id = queryData.warehouse_id
      _self.warehouse_zone_id = queryData.warehouse_zone_id
      _self.productivetask_id = queryData.productivetask_id
      _self.materilas = []
      for (let m of queryData.itemSelects) {
        m.warehouse_zone_id = _self.warehouse_zone_id
        _self.materilas.push(m)
      }
      _self.masterTable = $('#submitMaterielTable')

      function typeFormatter(value,row) {
        if (row.taskrelated_type) {
          return row.taskrelateddesign_number
        } else {
          return row.taskdesign_number
        }
      }

      function stockTypeFormatter(value,row) {
        if (row.taskrelated_type) {
          return row.related_stock_in_number
        } else {
          return row.stock_in_number
        }
      }

      function initTable () {
        _self.masterTable.bootstrapTable({
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormat('materiel_code', '物料编码'),
            common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
            common.BTRowFormat('materiel_format', '规格'),
            common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'materielInfo'),
            common.BTRowFormatEdSelectAlignLeft(_self, 'warehouse_zone_id', '仓区', 'wareHouseZoneInfo'),
            common.BTRowFormatWithFormatterAlignRight('', '需求数量',typeFormatter),
            common.BTRowFormatWithFormatterAlignRight('', '已入库数量',stockTypeFormatter),
            common.BTRowFormatEditableRight('stock_operate_amount', '入库数量', value => {
              return value || 0;
            })
          ],
          idField: 'materiel_id',
          uniqueId: 'materiel_id',
          toolbar: '#toolbar',
          striped: true,
          clickToSelect: true,
          locale: 'zh-CN',
          onEditableShown: (field, row, $el, editable) => {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: (field, row, oldValue, $el) => {
//            let remain = 0
//            if (row.taskrelated_type) {
//              remain = row.taskrelateddesign_number - row.related_stock_in_number
//            } else {
//              remain = row.taskdesign_number - row.stock_in_number;
//            }
//            console.log(remain);
//            let will = row.stock_operate_amount;
//            console.log(will);
//            let reg = /^[1-9]{1,}[\d]*$/;//正整数
//            let validate = reg.test(will);
//            if (!validate) {
//              common.dealWarningCommon('请输入正整数');
//              will = 0;
//            } else if (Number(will) > remain) {
//              common.dealWarningCommon('入库数量不能大于待入库数量');
//              will = 0
//            } else if (will <= 0) {
//              common.dealWarningCommon('请填写入库数量');
//            }
          }
        });
        common.changeTableClass(_self.masterTable)
      }

      async function initPage () {
        let response = await _self.$http.post(apiUrl + 'initDetail', {
          warehouse_id:_self.warehouse_id,
          warehouse_zone_id: _self.warehouse_zone_id
        });
        let retData = response.data.info;
        _self.pagePara = JSON.parse(JSON.stringify(retData));
        initTable()
        _self.masterTable.bootstrapTable('load', {
          data: queryData.itemSelects
        })
      }

      $(function() {
        initPage();
      })
    }
  }
</script>
<style scoped>
</style>
