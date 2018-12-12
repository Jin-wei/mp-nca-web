<template>
    <div>
        <div v-show="checkState('DESIGNING')">
            <div class="panel panel-inverse">
                <div class="panel-body">
                    <div class="form-group">
                        <div class="form-inline">
                            <div class="row" style="border: 1px solid #eee;padding: 20px">
                                <div class="form-group m-r-10">
                                    <div class="input-group">
                                        <span class="input-group-addon">总面积</span>
                                        <input disabled class="form-control" placeholder="请输入总面积" v-model="order.order_house_area">
                                    </div>
                                </div>
                                <div class="form-group m-r-10">
                                    <div class="input-group">
                                        <span class="input-group-addon">套餐产品</span>
                                        <select disabled class="form-control select2" id="produce_id"></select>
                                    </div>
                                </div>
                                <div class="form-group m-r-10">
                                    <div class="input-group">
                                        <span class="input-group-addon">预收订金</span>
                                        <input disabled class="form-control" placeholder="请输入预收订金" v-model="order.earnest">
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                    </div>
                    <h4 class="panel-title">设计文档</h4>
                </div>
                <div class="panel-body">
                    <table id="fileTable"></table>
                </div>
            </div>
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                    </div>
                    <h4 class="panel-title">3D效果图</h4>
                </div>
                <div class="panel-body">
                    <table id="VRTable"></table>
                </div>
            </div>
        </div>
        <div class="col-md-12" v-show="!checkState('DESIGNING')" >
            请分配订单人员
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/ordermanage/NCAHDOrderDetailControl?method=';

  export default {
    props: ['order', 'pagePara'],
    data: function() {
      return {
        workRow: {},
        userinfo: common.getStoreData('userinfo'),
        oldRow: '',
        uploadFiles: [],
        nowFileUrl: null,
        nowDesignId: null

      }
    },
    name: 'NCAHDDesignControl',
    mounted: function() {
      let _self = this;
    },
    watch: {
      pagePara: function() {},
      order: function() {
        let _self = this;
        let template = $('#produce_id');
        let $fileTable = $('#fileTable');
        let $VRTable = $('#VRTable');

        window.designEvents = {
          'change .fileupload': function(e, value, row, index) {
            common.fileFileUpload(this, _self, row, apiUrl, 'design_update', $fileTable, 'design_id')
          },
          'click .delete-button': function(e, value, row, index) {
            common.deleteFiles(this, _self, row, apiUrl, 'delete_file', $fileTable, 'design_id')
          },
          'click .VREdit': function(e, value, row, index) {
            _self.nowFileUrl = row.file_url;
            _self.nowDesignId = row.design_id;
            $('#editModal').modal('show');
          }
        }

        function trueOrFalseTypeFilter(value) {
          if (value == '1') {
            return '是'
          }
          return '否'
        }

        function initFileTable() {
          $fileTable.bootstrapTable({
            columns: [
              common.BTRowFormatWithIndex('NO.'),
              common.BTRowFormatAlignLeft('require_name', '文档名称'),
              common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
              common.BTRowFormatWithFormatter('require_hidden', '客户可见', trueOrFalseTypeFilter),
              common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
            ],
            idField: 'design_id',
            uniqueId: 'design_id',
            clickToSelect: true,
            locale: 'zh-CN',
            onPostBody: function() {
              $('[data-toggle="popover"]').each(function() {
                $(this).popover()
              })
            }
          })
          common.changeTableClass($fileTable)
        }

        function dataFormatter(value, row, index) {
          if (value && row.file_url) {
            var date = new Date(value);
            var dateStr = date.toLocaleString();
            return dateStr;
          }else {
            return null
          }
        }
        function userFormatter(value, row, index) {
          for (let employee of _self.pagePara.employeeInfo) {
            if (value == employee.id) {
              return employee.text;
            }
          }
          return null;
        }

        function initVRTable() {
          $VRTable.bootstrapTable({
            columns: [
              common.BTRowFormatWithIndex('NO.'),
              common.BTRowFormatAlignLeft('require_name', '效果图名称'),
              common.BTRowFormatWithFormatter('file_url', '效果图链接', common.linkFormatter),
              common.BTRowFormatWithFormatter('updated_at', '上传日期', dataFormatter),
              common.BTRowFormatWithFormatterAlignLeft('create_user_id', '创建人', userFormatter),
            ],
            idField: 'design_id',
            uniqueId: 'design_id',
            clickToSelect: true,
            locale: 'zh-CN',
              striped: true
          })
          common.changeTableClass($VRTable)
        }

        async function getTablesData() {
          try {
            let response = await _self.$http.post(apiUrl + 'search_order_design', {
              orderId: _self.order.order_id
            });
            let retdata = response.data.info;
            $('#fileTable').bootstrapTable('load', {
              data: retdata.designs
            });

            $('#VRTable').bootstrapTable('load', {
              data: retdata.vrs
            });
          } catch (error) {
            common.dealErrorCommon(_self, error);
          }
        }

        function initPage() {
          initFileTable()
          initVRTable()
          $('#formMeasure').parsley();
          $('#formVR').parsley();
          common.initSelect2Width($('#produce_id'), _self.pagePara.produceInfo, '151');
          common.initSelect2($('#measure_room'), _self.order.roomsInfo);
          common.initSelect2($('#measure_pillar'), _self.pagePara.tfInfo);
          common.initSelect2($('#has_bay_window'), _self.pagePara.tfInfo);
          common.initSelect2($('#has_downcomer'), _self.pagePara.tfInfo);
          common.initSelect2($('#srv_type'), _self.pagePara.fileTypeInfo);

          template.val(_self.order.produce_id).trigger('change');


          getTablesData(_self);
        }

        initPage();
      }
    },
    methods: {
      checkState: function(state, event) {
        let _self = this
        if (_self.order.statedict) {
          if (state in _self.order.statedict) return true
        }
        return false
      }
    }
  }
</script>
<style scoped>
</style>
