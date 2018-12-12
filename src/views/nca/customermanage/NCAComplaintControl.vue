<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">投诉管理</a></li>
            <li class="active">投诉列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'投诉列表'"></DomainChooseHeader>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_complaint" placeholder="搜索订单号、客户姓名" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search(compliantState)"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="showCreateModal">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="search(1)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">未受理</span>
                                    <span class="hidden-xs">未受理</span>
                                </a>
                            </li>
                            <li @click="search(2)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">已受理</span>
                                    <span class="hidden-xs">已受理</span>
                                </a>
                            </li>
                            <li @click="search(0)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">全部</span>
                                    <span class="hidden-xs">全部</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="complaintTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="complaintModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增客户投诉列表</h4>
                    </div>
                    <form @submit.prevent="createCompalint" id="formComplaint">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户姓名</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.complaint_customer_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户手机号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.complaint_phone" data-parsley-phone="true" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>订单号</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple style="width:100%" id="complaint_order_id" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>受理人</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple style="width:100%" id="complaint_responser" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12" style="padding-left: 5px">
                                <label class="col-sm-2 control-label"><span class="table-required">*</span>投诉内容</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" v-model="workRow.complaint_content" data-parsley-required="true" maxlength="200" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div class="modal fade" id="handleModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">受理投诉</h4>
                    </div>
                    <form @submit.prevent="handle" id="formHandle">
                        <div class="modal-body">
                            <div class="form-horizontal">
                                <div class="form-group">
                                    <label class="col-md-2 control-label">处置记录</label>
                                    <div class="col-md-10">
                                        <textarea class="form-control" rows="5"
                                                  id="complaint_handle_record"
                                                  data-parsley-required="true"
                                                  maxlength="300"
                                                  data-parsley-maxlength="300">
                                        </textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc')
  const apiUrl = '/api/nca/customermanage/NCAComplaintControl?method='

  import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'

  export default {
    data: function() {
      return {
        pagePara: '',
        workRow: {},
        compliantState: 1,
        apiName: common.getApiName(apiUrl),
        listTitle: ''
      }
    },
    name: 'NCAComplaintControl',
    components: {
      DomainChooseHeader
    },
    mounted: function() {
      let _self = this
      let $table = $('#complaintTable');

      function queryParams(params) {
        let domain_id = common.getSelect2Val('domain_select')
        if (domain_id) {
          params.domain_id = domain_id
        }
        params.search_complaint = $('#search_complaint').val()
        params.complaint_state = _self.compliantState
        params.current_type = _self.currentType
        return JSON.stringify(params)
      }

      function orderFormatter(value, row) {
        return [
          '<a class="show_order_detail" style="cursor: pointer;">',
          value,
          '</a>'
        ].join('')
      }

      function customerFormatter(value, row) {
        return [
          '<a class="show_customer_detail" style="cursor: pointer;">',
          value,
          '</a>'
        ].join('')
      }

        function handleFormatter(value, row) {
            if(row.complaint_state === '1'){
                return [
                    '<a class="btn btn-info btn-xs m-r-5 handle_complaint">受理投诉</a>'
                ].join('')
            }
        }

      window.tableEvents = {
        'click .show_order_detail': function(e, value, row, index) {
          if(row.order_type === '7') {
            _self.$router.push({
              path: '/nca/ordermanage/NCAGOrderDetailControl',
              query: {
                orderId: row.order_id
              }
            })
          } else {
            _self.$router.push({
              path: '/nca/ordermanage/NCAOrderDetailControl',
              query: {
                orderId: row.order_id
              }
            })
          }
        },
        'click .show_customer_detail': function(e, value, row, index) {
          let url = '/nca/customermanage/NCACustomerDetailControl?userId=' + row.complaint_user_id + '&offset=' + 0;
          _self.$router.push({
            path: url
          })
        },
        'click .handle_complaint': function(e, value, row, index) {
          _self.workRow = row
          $('#handleModal').modal('show')
          $('#formHandle').parsley().reset()
        }
      }

      function initTable() {
        $table.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('order_id', '订单号', orderFormatter, tableEvents),
            common.BTRowFormatWithFEL('complaint_customer_name', '客户姓名', customerFormatter, tableEvents),
            common.BTRowFormat('complaint_phone', '客户手机号'),
            common.BTRowFormatWithFormatterAlignLeft('complaint_content', '投诉内容',common.remarkFormatter),
            common.BTRowFormat('created_at', '提交时间'),
            common.BTRowFormatEdSelect2Disabled(_self, 'complaint_state', '状态', 'complaintStateInfo'),
            common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'complaint_responser', '受理人', 'staffInfo'),
            common.BTRowFormatWithFormatterAlignLeft('complaint_handle_record', '处置记录',common.remarkFormatter),
            common.actFormatter('handle', handleFormatter, tableEvents)
          ],
          sortOrder:'asc',
          idField: 'complaint_id',
          uniqueId: 'complaint_id',
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
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'complaint_id', $table)
          },
          onPostBody: function() {
              $('[data-toggle="popover"]').each(function() {
                  $(this).popover()
              })
          }
        });
        common.changeTableClass($table)
      }

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
          common.initSelect2Editable($('#complaint_order_id'), _self.pagePara.domainOrderInfo);
          common.initSelect2Editable($('#complaint_responser'), _self.pagePara.staffInfo);
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        $('#domain_select').on('change', function(evt) {
          _self.search(_self.compliantState)
        });
        initTable()
        $('#formComplaint').parsley()
        $('#formHandle').parsley()
      }

      $(async function() {
        await initPage();
      });
    },
    methods: {
      search: function(index) {
        let _self = this
        _self.compliantState = index
        $('#complaintTable').bootstrapTable("refresh")
      },
      showCreateModal: function(event) {
        let _self = this
        $('#formComplaint').parsley().reset()
        $('#complaint_responser').val(null).trigger('change');
        $('#complaintModal').modal('show')
      },
      createCompalint: function() {
        let _self = this
        if ($('#formComplaint').parsley().isValid()) {
          _self.workRow.order_id = common.getSelect2Val('complaint_order_id')
          _self.workRow.complaint_responser = common.getSelect2Val('complaint_responser')
          _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
            let retData = response.data.info
            _self.workRow = {}
            $('#complaintModal').modal('hide')
            $('#complaintTable').bootstrapTable("refresh")
            common.dealSuccessCommon('增加成功')
          }, (response) => {
            common.dealErrorCommon(_self, response)
          })
        }
      },
      handle: function () {
        let _self = this
        if ($('#formHandle').parsley().isValid()) {
          _self.workRow.complaint_handle_record = $('#complaint_handle_record').val()
          _self.$http.post(apiUrl + 'handle_complaint', _self.workRow).then((response) => {
            let retData = response.data.info
            _self.workRow = {}
            $('#handleModal').modal('hide')
            common.dealSuccessCommon('投诉已受理')
            $('#complaintTable').bootstrapTable("refresh")
          }, (response) => {
            common.dealErrorCommon(_self, response)
          })
        }
      }
    }
  }
</script>
<style scoped>
</style>
