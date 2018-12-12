<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">任务分配</a></li>
            <li class="active">工作流配置列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                  <div class="panel-heading">
                      <div class="panel-heading-btn">
                          <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                      </div>
                      <h4 class="panel-title">工作流配置列表</h4>
                  </div>
                  <div class="panel-body">
                    <ul class="nav nav-tabs">
                          <li class="active"><a href="#default-tab" @click="" data-toggle="tab">系统工作流</a>
                          </li>
                          <li><a href="#custom-tab" @click="" data-toggle="tab">自定义工作流</a>
                          </li>
                      </ul>
                      <div class="tab-content">
                          <div class="tab-pane fade active in" id="default-tab">
                              <table id="table"></table>
                          </div>
                          <div class="tab-pane fade" id="custom-tab">
                            <div class="panel-heading-btn" style="margin-bottom: 15px">
                                <button class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                            <table id="customtable"></table>
                          </div>
                      </div>
                  </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="Modify">
            <div class="modal-dialog" style="width:900px;">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">选择人员</h4>
                    </div>
                    <div class="modal-body row">
                      <div class="col-md-3 modal-height">
                        <ul id="selectTree" class="ztree"></ul>
                      </div>
                      <div class="col-md-9">
                          <div class="col-md-5 modal-height">
                              <table id="groupUserTable"></table>
                          </div>
                          <div class="col-md-1 modal-height vertical-container">
                              <div class="btn-group-vertical">
                                  <button type="button" class="btn btn-primary btn-icon" @click="addusers"><i class="icon-arrow-right"></i></button>
                              </div>
                          </div>
                          <div class="col-md-5 modal-height">
                              <p>
                              <button type="button" class="btn btn-primary btn-xs m-r-5" @click="addlevel">增加审核级别</button>
                              <button type="button" class="btn btn-danger btn-xs m-r-5" @click="dellevel">删除审核级别</button>
                              </p>
                              <table id="resultUserTable"></table>
                          </div>
                      </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">增加自定义工作流</h4>
                    </div>
                    <div class="modal-body">
                      <div class="form-group">
                          <label>任务名称</label>
                          <input class="form-control" v-model="workRow.customtaskallot_name">
                      </div>
                      <div class="form-group">
                          <label>描述</label>
                          <input class="form-control" v-model="workRow.customtaskallot_describe">
                      </div>
                    </div>
                    <div class="modal-footer">
                      <button type="button" class="btn btn-info" v-on:click="addCustomTask">保存</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCATaskAllotControl?method='
const UApiUrl = '/api/common/components/userSelectDialogControl?method=';

export default {
  data: function () {
    return {
      workRow: {},
      currentIdx: -1,
      workTable: '',
    }
  },
  name: 'NCATaskAllotControl',
  mounted: async function () {
    let _self = this
    let $table = $('#table')
    let $customtable = $('#customtable')
    let $groupUserTable = $('#groupUserTable')

    window.tableEvents = {
      'click .edit_detail': async function (e, value, row, index) {
        _self.workRow = row
        let response = await _self.$http.post(apiUrl + 'search_t', { taskallot_id: _self.workRow.taskallot_id, customtaskallot_id: _self.workRow.customtaskallot_id });
        _self.workRow.allot = response.data.info
        _self.initResultUserTable()
        $('#Modify').modal('show')
      },
      'click .userSelectDelete': function (e, value, row, index) {
        common.dealConfrimCommon('删除', function () {
          _self.$http.post(apiUrl + 'delete', {
            taskallotuser_id: row.taskallotuser_id
          }).then((response) => {
            _self.workTable.bootstrapTable('remove', {
              field: 'user_id',
              values: [row.user_id]
            });
            common.dealSuccessCommon('删除成功');
            console.log('delete success');
          }, (response) => {
            console.log('delete error');
            common.dealErrorCommon(_self, response);
          });
        });
      },
      'click .customDelete': function (e, value, row, index) {
        common.rowDeleteWithApi(_self, ' 工作流删除', apiUrl + 'delete_custome_allot', $customtable, row, 'customtaskallot_id')
      }
    }

    async function zTreeOnClick(event, treeId, treeNode) {
      if (treeNode.node_type === '01') {
        try {
          let response = await _self.$http.post(UApiUrl + 'searchUser', {
            usergroup_id: treeNode.usergroup_id
          });
          $groupUserTable.bootstrapTable('load', {
            data: response.data.info
          })

        } catch (error) {
          common.dealErrorCommon(_self, error);
        }
      }
    }

    async function getTreeData() {
      try {
        let response = await _self.$http.post(UApiUrl + 'search', {});
        let zNodes = response.data.info;
        $.fn.zTree.destroy("#selectTree");
        let treeObj = $.fn.zTree.init($("#selectTree"), {
          callback: {
            onClick: zTreeOnClick
          }
        }, zNodes);
        treeObj.expandAll(true);
      } catch (error) {
        common.dealErrorCommon(_self, error);
      }
    }

    $groupUserTable.bootstrapTable({
      height: 370,
      columns: [{
        field: 'state',
        checkbox: true
      },
      common.BTRowFormatWithIndex('NO.'),
      common.BTRowFormatAlignLeft('name', '姓名'),
      common.BTRowFormat('phone', '手机')
      ],
      idField: 'user_id',
      uniqueId: 'user_id',
      clickToSelect: true,
      formatNoMatches: function () {
        return
      }
    })

    function queryParams(params) {
      return JSON.stringify(params)
    }

    function actFormatter(value, row) {
      return [
        '<a class="btn btn-info btn-xs m-r-5 edit_detail">分配审核人员</a>'
      ].join('')
    }

    $table.bootstrapTable({
      method: 'POST',
      url: apiUrl + 'search_allot',
      queryParams: queryParams,
      sidePagination: 'server',
      ajaxOptions: common.bootstrapTableAjaxOtions,
      responseHandler: function (res) {
        return res.info;
      },
      height: common.getTableHeight(),
      columns: [
        common.BTRowFormatWithIndex('NO.'),
        common.BTRowFormatAlignLeft('taskallot_name', '任务名称'),
        common.BTRowFormatAlignLeft('taskallot_describe', '描述'),
        common.actFormatter('act', actFormatter, tableEvents),
      ],
      idField: 'taskallot_id',
      uniqueId: 'taskallot_id',
      striped: true,
      clickToSelect: true,
      showRefresh: false,
      pagination: true,
      pageSize: common.pageSize(),
      pageList: [10, 15, 25, 50, 100],
      locale: 'zh-CN'
    })

    $customtable.bootstrapTable({
      method: 'POST',
      url: apiUrl + 'search_custome_allot',
      queryParams: queryParams,
      sidePagination: 'server',
      ajaxOptions: common.bootstrapTableAjaxOtions,
      responseHandler: function (res) {
        return res.info;
      },
      height: common.getTableHeight(),
      columns: [
        common.BTRowFormatWithIndex('NO.'),
        common.BTRowFormatAlignLeft('customtaskallot_name', '任务名称'),
        /*common.actFormatter('act', actFormatter, tableEvents),*/
        common.BTRowFormatWithFormatterAlignLeft('customtaskallot_describe', '描述',common.remarkFormatter),
        common.actFormatter('del', () => {
          return [
            '<a class="btn btn-info btn-xs m-r-5 edit_detail">分配审核人员</a>'+
            '<a class="btn btn-info btn-xs m-r-5 customDelete">删除</a>'
          ].join('')
        }, tableEvents)
      ],
      idField: 'customtaskallot_id',
      uniqueId: 'customtaskallot_id',
      striped: true,
      clickToSelect: true,
      showRefresh: false,
      pagination: true,
      pageSize: common.pageSize(),
      pageList: [10, 15, 25, 50, 100],
      locale: 'zh-CN',
      onPostBody: function () {
           $('[data-toggle="popover"]').each(function () {
               $(this).popover()
           })
      }
    })

    await getTreeData()
    common.reSizeCall()
  },
  methods: {
    addusers: function (event) {
      let _self = this,
        checkUsers = [],
        resultUsers = [],
        addUsers = [];
      if (_self.currentIdx < 0) {
        return common.dealWarningCommon('请选择要操作的审核级别')
      }
      let $groupUserTable = $('#groupUserTable')
      checkUsers = $groupUserTable.bootstrapTable('getAllSelections')
      resultUsers = _self.workTable.bootstrapTable('getData')
      for (let i = 0; i < checkUsers.length; i++) {
        let addFlag = true
        for (let j = 0; j < resultUsers.length; j++) {
          if (checkUsers[i].user_id === resultUsers[j].user_id) {
            addFlag = false
            break
          }
        }
        if (addFlag) {
          addUsers.push(checkUsers[i])
        }
      }

      _self.$http.post(apiUrl + 'add', {
        taskallot_id: _self.workRow.taskallot_id,
        customtaskallot_id: _self.workRow.customtaskallot_id,
        taskallotuser_level: _self.currentIdx,
        users: addUsers
      }).then(async (response) => {
        let currentIdx = _self.currentIdx
        let rsp = await _self.$http.post(apiUrl + 'search_t', { taskallot_id: _self.workRow.taskallot_id, customtaskallot_id: _self.workRow.customtaskallot_id });
        _self.workRow.allot = rsp.data.info
        _self.initResultUserTable()
        if(currentIdx > 0){
          _self.currentIdx = currentIdx
          $('#resultUserTable').bootstrapTable('expandRow', _self.currentIdx)
        }
      }, (response) => {
        console.log('add error');
        common.dealErrorCommon(_self, response);
      });
    },
    initResultUserTable: async function (event) {
      let _self = this
      let $resultUserTable = $('#resultUserTable')
      function removeFormatter(value, row, index) {
        return [
          '<a class="userSelectDelete" title="删除">',
          '<i class="glyphicon glyphicon-remove"></i>',
          '</a>',
        ].join('')
      }
      $resultUserTable.bootstrapTable('destroy')
      if (_self.workRow.allot.maxlevel === 0) {
        _self.currentIdx = 0
        _self.workTable = $resultUserTable
        $resultUserTable.bootstrapTable({
          height: 340,
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatAlignLeft('name', '姓名'),
            common.BTRowFormat('phone', '手机'),
            common.BTRowFormat('position', '职位'),
            common.actFormatter('act', removeFormatter, tableEvents)
          ],
          idField: 'user_id',
          uniqueId: 'user_id',
          clickToSelect: true,
          formatNoMatches: function () {
            return
          }
        })
        $resultUserTable.bootstrapTable('load', _self.workRow.allot.data[0])
      } else {
        _self.currentIdx = -1
        let rows = []
        for (let i = 0; i < _self.workRow.allot.maxlevel + 1; i++) {
          rows.push({ level: i })
        }
        function expandTable(row, $detail) {
          var $el = $detail.html('<table></table>').find('table')
          var data = []
          _self.currentIdx = row.level
          _self.workTable = $el
          $el.bootstrapTable({
            columns: [
              common.BTRowFormatWithIndex('NO.'),
              common.BTRowFormat('name', '姓名'),
              common.BTRowFormat('phone', '手机'),
              common.BTRowFormat('position', '职位'),
              common.actFormatter('act', removeFormatter, tableEvents)
            ],
            data: _self.workRow.allot.data[_self.currentIdx],
            idField: 'user_id',
            uniqueId: 'user_id',
            clickToSelect: true,
            formatNoMatches: function () {
              return
            }
          })
        }
        $resultUserTable.bootstrapTable({
          height: 340,
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormat('level', '审核级别'),
          ],
          idField: 'level',
          uniqueId: 'level',
          clickToSelect: true,
          detailView: true,
          onExpandRow: function (index, row, $detail) {
            if (_self.currentIdx !== index) {
              let allRows = $resultUserTable.bootstrapTable('collapseRow', _self.currentIdx)
            }
            expandTable(row, $detail)
          },
          onCollapseRow: function (index, row) {
            _self.currentIdx = -1
          },
          formatNoMatches: function () {
            return
          }
        })
        $resultUserTable.bootstrapTable('load', rows)
      }
    },
    addlevel: function (event) {
      let _self = this
      _self.workRow.allot.maxlevel += 1
      _self.workRow.allot.data.push([])
      _self.initResultUserTable()
    },
    dellevel: function (event) {
      let _self = this
      if (_self.currentIdx < 0) {
        return common.dealWarningCommon('请选择要操作的审核级别')
      }
      common.dealConfrimCommon('删除审核级别: ' + _self.currentIdx, function () {
        _self.$http.post(apiUrl + 'deleteLevel', {
          taskallot_id: _self.workRow.taskallot_id,
          customtaskallot_id: _self.workRow.customtaskallot_id,
          taskallotuser_level: _self.currentIdx
        }).then(async (response) => {
          let res = await _self.$http.post(apiUrl + 'search_t', { taskallot_id: _self.workRow.taskallot_id, customtaskallot_id: _self.workRow.customtaskallot_id });
          _self.workRow.allot = res.data.info
          _self.initResultUserTable()
        }, (response) => {
          common.dealErrorCommon(_self, response);
        });
      });
    },
    addM: function () {
      let _self = this
      _self.workRow = {}
      $("#AddModal").modal('show');
    },
    addCustomTask: async function () {
      let _self = this
      try {
        await this.$http.post(apiUrl + 'add_custome_allot', _self.workRow);
        common.dealSuccessCommon('添加成功');
        $('#customtable').bootstrapTable("refresh");
        $("#AddModal").modal('hide');
      } catch (error) {
        common.dealErrorCommon(_self, error)
      }
    }
  }
}
</script>
<style scoped>
.modal-height {
  height: 400px;
  overflow: auto;
}
</style>
