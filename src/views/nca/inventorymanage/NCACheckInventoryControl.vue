<!--盘点管理-->
<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">库存管理</a></li>
            <li class="active">盘点单列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">盘点单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" id="check_code" placeholder="请输入盘点单号"/>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间"/>
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间"/>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="gotoDetail">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="search(1)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">新任务</span>
                                    <span class="hidden-xs">新任务</span>
                                </a>
                            </li>
                            <li @click="search(2)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">待审批</span>
                                    <span class="hidden-xs">待审批</span>
                                </a>
                            </li>
                            <li @click="search(3)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">完成</span>
                                    <span class="hidden-xs">完成</span>
                                </a>
                            </li>
                            <li @click="search(-1)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">全部</span>
                                    <span class="hidden-xs">全部</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="checkInventoryTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="ImportModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">上传盘点结果</h4>
                    </div>

                    <div class="modal-body">
                        <div class="form-group col-md-12">
                            <label>选择文件：</label><span>（请选.csv文件）</span>
                            <div>
                                <input type="file" id="file" class="form-control input-sm " accept=".csv" size="1">
                                <input type="text" id="select1" hidden>
                                <!--<span class="margin-top-ten">{{filePath}}</span>-->
                            </div>
                        </div>

                    </div>
                    <div class="modal-footer" style="border-top:none">
                        <button type="button" class="btn btn-primary btn-info" id="importAct" v-on:click="importAct"><i class="fa fa-fw fa-plus"></i>上传</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCACheckInventoryControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: '',
        checkInventoryId: '',
        currentState: 1,
        fileUrl: '',
        filePath: ''
      }
    },
    name: 'NCACheckInventoryControl',
    mounted: function() {
      let _self = this;
      let $table = $('#checkInventoryTable')

      function queryParams(params) {
        if($('#check_code').val()){
          params.checkinventory_id = $('#check_code').val()
        }
        if($('#start_date').val()){
          params.created_at_start = $('#start_date').val()
        }
        if($('#end_date').val()){
          params.created_at_end = $('#end_date').val()
        }
        if(_self.currentState > 0){
          params.checkInventory_state = _self.currentState
        }
        return JSON.stringify(params)
      }

      function operateFormatter(value, row, index) {
        if (row.checkInventory_state == 1){
          return [
            '<a class="btn btn-info btn-xs m-r-5 download">下载</a>',
            '<a class="btn btn-info btn-xs m-r-5 upload">上传结果</a>',
          ].join('')
        } else {
          return [
            '<a class="btn btn-info btn-xs m-r-5 detail">查看</a>',
          ].join('')
        }
      }

      window.tableEvents = {
        'click .download': function(e, value, row, index) {
          _self.$http.post(apiUrl + 'export_check_inventory', {}).then((response) => {
            let downUrl = response.data.info;
            window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
          }, (response) => {
            common.dealErrorCommon(_self, response)
          })
        },
        'click .upload': function(e, value, row, index) {
          $('#ImportModal').modal('show')
          _self.workRow = row
        },
        'click .detail': function(e, value, row, index) {
          _self.checkInventoryId = row.checkinventory_id
          showDetail()
        }
      };

      function showDetail () {
        _self.$router.push({
          path: 'NCACheckInventoryDetailControl',
          query: {
            checkInventoryId: _self.checkInventoryId
          }
        })
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
            common.BTRowFormat('checkinventory_id', '盘点单号'),
            common.BTRowFormatAlignLeft('warehouse_name', '仓库名称'),
            common.BTRowFormatEdSelect2Disabled(_self, 'checkInventory_state', '状态', 'stateInfo'),
            common.BTRowFormat('check_plan_date', '计划盘点时间'),
            common.BTRowFormatAlignLeft('check_checker', '盘点人'),
            common.BTRowFormat('check_actual_date', '盘点时间'),
            common.BTRowFormatAlignLeft('check_reviewer', '审批人'),
            common.BTRowFormat('check_review_date', '审批时间'),
            common.actFormatter('operation', operateFormatter, tableEvents)
          ],
          idField: 'checkinventory_id',
          uniqueId: 'checkinventory_id',
          striped: true,
          clickToSelect: true,
          pagination: false,
          pageSize:common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: function(field, row, $el, editable) {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: function(field, row, oldValue, $el) {
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'checkinventory_id', $table)
          }
        });
        common.changeTableClass($table)
      }

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        initTable()
      }

      $(async function() {
        await initPage()
        common.initDatepicker($('#start_date'));
        common.initDatepicker($('#end_date'));
      });

      $('#file').change(function() {
        let files = this.files;
        let formData = new FormData();
        formData.append('file', files[0]);
        _self.filePath = $('#file').val();
        if (files.length > 0){
          _self.$http.post(apiUrl + 'upload', formData).then((response) => {
            if (response.data.errno == 0) {
              _self.fileUrl = response.data.info.uploadurl.url;
            } else {
              common.dealErrorCommon(_self, response)
            }
          }, (response) => {
            common.dealErrorCommon(_self, response)
          })
        }
      });
    },
    methods: {
      search: function(index) {
        let _self = this
        if (index) {
          _self.currentState = index
        }
        $('#checkInventoryTable').bootstrapTable("refresh", _self.queryParams)
      },
      gotoDetail: function () {
        let _self = this
        _self.$router.push({
          path: 'NCACheckInventoryDetailControl',
          query: {
            checkInventoryId: _self.checkInventoryId
          }
        })
      },
      importAct: function(event) {
        let _self = this;
        if (!_self.fileUrl) {
          return common.dealWarningCommon('请选择文件')
        }

        var params = {
          uploadurl: _self.fileUrl,
          checkinventory_id: _self.workRow.checkinventory_id,
          materiel_id: _self.workRow.materiel_id
        }
        _self.$http.post(apiUrl + 'import_check_inventory', params).then((response) => {
          let retData = response.data.info;
          common.dealSuccessCommon('上传成功');
            $('#checkInventoryTable').bootstrapTable("refresh", _self.queryParams)
        }, (response) => {
          common.dealErrorCommon(_self, response)
        });

        $('#ImportModal').modal('hide')
      },
    }
  }
</script>
<style scoped>
</style>
