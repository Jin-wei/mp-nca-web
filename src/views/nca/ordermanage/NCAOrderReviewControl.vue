<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单管理</a></li>
            <li class="active">订单评审</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">订单评审</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索订单号" style="width: 210px;">
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="search()">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="search(1)">
                                <a href="#fitTab" data-toggle="tab">
                                    <span class="visible-xs">装修订单</span>
                                    <span class="hidden-xs">装修订单</span>
                                </a>
                            </li>
                            <li class="" @click="search(2)">
                                <a href="#individualTab" data-toggle="tab">
                                    <span class="visible-xs">个人订单</span>
                                    <span class="hidden-xs">个人订单</span>
                                </a>
                            </li>
                            <li class="" @click="search(3)">
                                <a href="#enterpriseTab" data-toggle="tab">
                                    <span class="visible-xs">企业订单</span>
                                    <span class="hidden-xs">企业订单</span>
                                </a>
                            </li>
                            <li class="" @click="search(4)">
                                <a href="#organizationTab" data-toggle="tab">
                                    <span class="visible-xs">机构订单</span>
                                    <span class="hidden-xs">机构订单</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fitTab">
                                <table id="fitTable"></table>
                            </div>
                            <div class="tab-pane fade" id="individualTab">
                                <table id="individualTable"></table>
                            </div>
                            <div class="tab-pane fade" id="enterpriseTab">
                                <table id="enterpriseTable"></table>
                            </div>
                            <div class="tab-pane fade" id="organizationTab">
                                <table id="organizationTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc')
  const apiUrl = '/api/nca/ordermanage/NCAOrderReviewControl?method='

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: '',
        orderType: 1
      }
    },
    name: 'NCAOrderReviewControl',
    mounted: function() {
      let _self = this
      let $fitTable = $('#fitTable')
      let $individualTable = $('#individualTable')
      let $enterpriseTable = $('#enterpriseTable')
      let $organizationTable = $('#organizationTable')

      function nameFormatter(value, row) {
        return [
          '<a class="show_detail" style="cursor: pointer;">',
          value,
          '</a>'
        ].join('')
      }

      window.tableEvents = {
        'click .show_detail': function(e, value, row, index) {
          _self.$router.push({
            path: '/nca/ordermanage/NCAOrderReviewDetailControl',
            query: {
              orderId: row.order_id
            }
          })
        }
      };

      function queryParams(params) {
        if ($('#search_text').val()) {
          params.search_text = $('#search_text').val()
        }
        if ($('#start_date').val()) {
          params.createdBTime = $('#start_date').val()
        }
        if ($('#end_date').val()) {
          params.createdETime = $('#end_date').val()
        }
        return JSON.stringify(params);
      }
      //装修订单
      function initFitTable() {
        $fitTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_fit_reviews',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
            common.BTRowFormatAlignLeft('name', '客户姓名'),
            common.BTRowFormat('phone', '客户手机'),
            common.BTRowFormat('create_date', '创建日期')
          ],
          idField: 'order_id',
          uniqueId: 'order_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN'
        })
        common.changeTableClass($fitTable)
      }

      function saleQueryParams(params) {
        if ($('#search_text').val()) {
          params.search_text = $('#search_text').val()
        }
        if (_self.orderType == 2) {
          params.purchaser_type = 2;
        } else if (_self.orderType == 3) {
          params.purchaser_type = 3;
        } else if (_self.orderType == 4) {
          params.purchaser_type = 1;
        }
        if ($('#start_date').val()) {
          params.createdBTime = $('#start_date').val()
        }
        if ($('#end_date').val()) {
          params.createdETime = $('#end_date').val()
        }
        return JSON.stringify(params);
      }
      //个人订单
      function initIndividualTable() {
        $individualTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_sale_reviews',
          queryParams: saleQueryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
            common.BTRowFormatEdSelect2Disabled(_self, 'purchaser_type', '采购方类型', 'clientTypeInfo'),
            common.BTRowFormatAlignLeft('name', '客户姓名'),
            common.BTRowFormat('phone', '客户联系电话'),
            common.BTRowFormat('create_date', '创建日期')
          ],
          idField: 'order_id',
          uniqueId: 'order_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN'
        })
        common.changeTableClass($individualTable)
      }
      //企业订单
      function initEnterpriseTable() {
        $enterpriseTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_sale_reviews',
          queryParams: saleQueryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
            common.BTRowFormatEdSelect2Disabled(_self, 'purchaser_type', '采购方类型', 'clientTypeInfo'),
            common.BTRowFormatAlignLeft('corporateclients_name', '企业名称'),
            common.BTRowFormat('corporateclients_phone', '联系电话'),
            common.BTRowFormat('create_date', '创建日期')
          ],
          idField: 'order_id',
          uniqueId: 'order_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN'
        })
        common.changeTableClass($enterpriseTable)
      }
      //机构订单
      function initOrganizationTable() {
        $organizationTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_sale_reviews',
          queryParams: saleQueryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
            common.BTRowFormatEdSelect2Disabled(_self, 'purchaser_type', '采购方类型', 'clientTypeInfo'),
            common.BTRowFormatAlignLeft('domain_name', '机构名称'),
            common.BTRowFormat('domain_phone', '联系电话'),
            common.BTRowFormatAlignLeft('domain_contact', '联系人'),
            common.BTRowFormat('create_date', '创建日期')
          ],
          idField: 'order_id',
          uniqueId: 'order_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN'
        })
        common.changeTableClass($organizationTable)
      }

      async function getInitData () {
        let response = await _self.$http.post(apiUrl + 'init', {})
        let retData = response.data.info
        _self.pagePara = JSON.parse(JSON.stringify(retData))
      }

      async function initPage() {
        await getInitData()
        common.initDatepicker($('#start_date'))
        common.initDatepicker($('#end_date'))
        initFitTable()
        initIndividualTable()
        initEnterpriseTable()
        initOrganizationTable()
      }

      $(function() {
        initPage()
      })
    },
    methods: {
      search: function(index) {
        let _self = this
        if (index) {
          _self.orderType = index
        }
        if (_self.orderType == 1) {
          $('#fitTable').bootstrapTable("refresh");
        } else if (_self.orderType == 2) {
          $('#individualTable').bootstrapTable("refresh");
        } else if (_self.orderType == 3) {
          $('#enterpriseTable').bootstrapTable("refresh");
        } else if (_self.orderType == 4) {
          $('#organizationTable').bootstrapTable("refresh");
        }
      }
    }
  }
</script>
<style scoped>
</style>
