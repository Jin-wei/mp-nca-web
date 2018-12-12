<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">事务管理</a></li>
            <li class="active">任务列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">任务列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="task_type" style="width: 150px">
                                        <option value="">请选任务类型</option>
                                        <option v-for="type in pagePara.taskTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="task_state" style="width: 150px">
                                        <option value="">请选状态</option>
                                        <option v-for="state in pagePara.taskStateInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间"/>
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间"/>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="search(currentType)">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
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
                                    <span class="visible-xs">收到的任务</span>
                                    <span class="hidden-xs">收到的任务</span>
                                </a>
                            </li>
                            <li @click="search(2)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">发布的任务</span>
                                    <span class="hidden-xs">发布的任务</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="taskListTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="createTaskModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新建任务列表</h4>
                    </div>
                    <form @submit.prevent="submitTask" id="formTask">
                        <div class="modal-body row">
                            <div class="form-group col-sm-12 p_r">
                                <label class="col-sm-2 control-label">任务类型</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2" multiple id="custom_task_select"></select>
                                </div>
                            </div>
                                <div class="form-group col-sm-12 p_r">
                                    <label class="col-sm-2 control-label">任务描述</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" id="task_description" maxlength="300" data-parsley-maxlength="300"></textarea>
                                    </div>
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
  const common = require('commonFunc');
  const apiUrl = '/api/nca/baseconfig/NCATaskListControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: '',
        currentType: 1,
        userinfo: common.getStoreData('userinfo')
      }
    },
    name: 'NCATaskListControl',
    mounted: function() {
      let _self = this;
      let $table = $('#taskListTable');

      function queryParams(params) {
        if ($('#task_type').val()){
          params.task_type = $('#task_type').val()
        }
        if ($('#task_state').val()){
          params.task_state = $('#task_state').val()
        }
        if ($('#start_date').val()){
          params.created_at_start = $('#start_date').val()
        }
        if ($('#end_date').val()){
          params.created_at_end = $('#end_date').val()
        }
        params.domain_id = _self.userinfo.domain_id;
        params.current_type = _self.currentType
        return JSON.stringify(params);
      }

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
            path: '/nca/baseconfig/NCATaskDetailControl',
            query: {
              taskId: row.task_id
            }
          })
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
            common.BTRowFormatWithFE('task_id', '任务编号', nameFormatter, tableEvents),
            common.BTRowFormatAlignLeft('task_name', '任务名称'),
            common.BTRowFormatEdSelect2Disabled(_self, 'task_type', '任务类型', 'taskTypeInfo'),
            common.BTRowFormatEdSelect2Disabled(_self, 'task_priority', '优先级', 'taskPriorityInfo'),
            common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'task_performer', '执行人', 'staffInfo'),
            common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'task_publisher', '发布人', 'staffInfo'),
            common.BTRowFormatEdSelect2Disabled(_self, 'task_state', '状态', 'taskStateInfo'),
            common.BTRowFormat('task_create_date', '创建日期'),
            common.BTRowFormat('task_complete_date', '完成日期')
          ],
          sortOrder:'asc',
          idField: 'task_id',
          uniqueId: 'task_id',
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
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'task_id', $table)
          }
        });
        common.changeTableClass($table)
        $('#taskListTable').bootstrapTable('hideColumn', 'task_performer');
        $('#taskListTable').bootstrapTable('showColumn', 'task_publisher');
      }

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
          common.initSelect2($('#custom_task_select'), _self.pagePara.customtaskInfo);
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        initTable()
      }

      $(async function() {
        await initPage();
        common.initDatepicker($('#start_date'));
        common.initDatepicker($('#end_date'));
//        common.initDatepicker($('#end_time'));
      });
    },
    methods: {
      showCreateModal: function () {
        let _self = this
        $('#custom_task_select').val(null).trigger('change')
        $('#formTask').parsley().reset()
        $('#createTaskModal').modal('show')
      },
      submitTask: function() {
        let _self = this
        if ($('#formTask').parsley().isValid()) {
          let param = {
            customtaskallot_id: common.getSelect2Val('custom_task_select'),
            task_description: $('#task_description').val()
          }
          _self.$http.post(apiUrl + 'add', param).then((response) => {
            let retData = response.data.info
            common.dealSuccessCommon('任务创建成功')
            $('#taskListTable').bootstrapTable("refresh", _self.queryParams)
          },(error) =>{
             common.dealErrorCommon(_self, error)
          })
          $('#createTaskModal').modal('hide')
        }
      },
      search: function(index) {
        let _self = this
        if (index) {
          _self.currentType = index
        }
        $('#taskListTable').bootstrapTable("refresh", _self.queryParams)

        if (_self.currentType == 1) {
          $('#taskListTable').bootstrapTable('hideColumn', 'task_performer')
          $('#taskListTable').bootstrapTable('showColumn', 'task_publisher')
        } else {
          $('#taskListTable').bootstrapTable('showColumn', 'task_performer')
          $('#taskListTable').bootstrapTable('hideColumn', 'task_publisher')
        }
      }
    }
  }
</script>
<style scoped>
    .p_r{
        padding-right: 30px;
        padding-left: 0px;
    }
</style>
