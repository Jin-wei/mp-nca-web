<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单管理</a></li>
            <li class="active">收款规则列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收款规则列表</h4>
                    </div>
                    <div class="panel-toolbar col-md-12">
                        <div class="form-inline">
                            <div class="form-group  pull-right">
                                <button class="btn btn-info" @click="createRule">增加</button>
                                <button class="btn btn-info" @click="distributeRule">发布</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="ruleTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="createModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">增加收款项</h4>
                    </div>
                    <form @submit.prevent="createAct" id="ruleForm">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>收款项名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.receivables_rule_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>收款比例</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" type="number" min="0" max="100" step="0.1" v-model="workRow.receivables_rule_rate" data-parsley-required="true">
                                    </div>
                                    <label class="col-sm-1 control-label">%</label>
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
  const apiUrl = '/api/nca/ordermanage/NCAReceivablesRuleControl?method='

  export default {
    data: function() {
      return {
        pagePara: '',
        workRow: {},
        rules: []
      }
    },
    name: 'NCAReceivablesRuleControl',
    mounted: function() {
      let _self = this

      let $ruleTable = $('#ruleTable');

      function queryParams(params) {
        return JSON.stringify(params)
      }

      window.tableEvents = {
        'click .delete': function(e, value, row, index) {
            _self.rules.splice(index, 1);
            $('#ruleTable').bootstrapTable('load', {
                data: _self.rules,
            })
        }
      };

      function rateFormatter(value, row, index) {
        return (value*100).toFixed(1) + '%';
      }

      function initRuleTable() {
        $ruleTable.bootstrapTable({
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatAlignLeft('receivables_rule_name', '收款项名称'),
            common.BTRowFormatWithFormatterAlignRight('receivables_rule_rate', '收款比例',rateFormatter),
            common.actFormatter('act', () => {
              return '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>';
            }, tableEvents)
          ],
          idField: 'receivablesrule_id',
          uniqueId: 'receivablesrule_id',
          striped: true,
          clickToSelect: true,
          showRefresh: false,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN'
        });
        common.changeTableClass($ruleTable)
      }
      
      async function getRules () {
        let response = await _self.$http.post(apiUrl + 'search_rule', {});
        _self.rules = response.data.info;
        $('#ruleTable').bootstrapTable('load', {
          data: _self.rules,
        })
      }

      async function initPage() {
        initRuleTable();
        await getRules();
      }

      initPage();
    },
    methods: {
      createRule: function() {
        let _self = this;
        _self.workRow = {};
        $('#ruleForm').parsley().reset();
        $('#createModal').modal('show');
      },
      distributeRule: async function() {
        let _self = this;
        //校验比例总和
        let totalRate = 0;
        for(let r of _self.rules){
          totalRate += r.receivables_rule_rate;
        }
        if (totalRate > 1) {//发布比例总和不能超过100%
          common.dealWarningCommon('收款比例总和已超过100%');
          return
        } else if (totalRate > 0 && totalRate < 1) {
          common.dealWarningCommon('收款比例总和不足100%');
          return
        } else if (totalRate == 0) {
          common.dealWarningCommon('请增加收款项');
          return
        }
        //发布规则
        try {
          let params = {
            rules: _self.rules
          }
          let response = await _self.$http.post(apiUrl + 'distribute_rule', params)
          common.dealSuccessCommon('收款规则发布成功');
          $('#createModal').modal('hide');
          $('#ruleTable').bootstrapTable("refresh");
        } catch (error) {
          common.dealErrorCommon(_self, error);
        }
      },
      createAct: function () {
        let _self = this;
        if (_self.validate()){
            common.dealWarningCommon(_self.validate())
            return;
        }
        if ($('#ruleForm').parsley().isValid()) {
            let obj = {
              receivables_rule_name: _self.workRow.receivables_rule_name,
              receivables_rule_rate: (_self.workRow.receivables_rule_rate)/100
            }
            _self.rules.push(obj);
            _self.workRow = {};
            $('#createModal').modal('hide');
        }
        $('#ruleTable').bootstrapTable('load', {
          data: _self.rules,
        })
      },
      validate: function () {
        let _self = this;
        let errorCode = '';
        let totalRate = 0;
        for(let r of _self.rules){
          if (_self.workRow.receivables_rule_name == r.receivables_rule_name){
            errorCode = '项目名称重复';
            break;
          }
          totalRate += r.receivables_rule_rate;
        }
        if (totalRate > 1) {
          errorCode = '收款比例总和已超过100%';
        }
        let remain = (100 - Number(totalRate)*100 - Number((_self.workRow.receivables_rule_rate)));
        if (remain < 0){
          errorCode = '新增项目收款比例不符合要求';
        }
        return errorCode;
      }
    }
  }
</script>
<style scoped>
</style>
