<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">人力需求管理</a></li>
            <li class="active">人力需求管理</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs"  href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">发布人</span>
                                    <input class="form-control" v-model="hrDetail.username" data-parsley-required="true" data-parsley-maxlength="60" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">发布时间</span>
                                    <input class="form-control" v-model="hrDetail.created_at" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">岗位名称</span>
                                    <input class="form-control" v-model="hrDetail.position_name" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">部门</span>
                                    <input class="form-control" v-model="hrDetail.department_name" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <div class="input-group ">
                                    <span class="input-group-addon">详情</span>
                                    <div>
                                        <textarea rows="5" class="form-control" style="resize:none;"  v-model="hrDetail.hr_remark" id="detail" disabled></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/baseconfig/NCAHumanResourceControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        hr_id: null,
        hrDetail: {}
      }
    },
    name: 'NCANoticeDetailControl',
    mounted: function() {
      let _self = this;
      _self.hr_id = _self.$route.query.hr_id

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function getNotice() {
        try {
          let param = {
              hr_id: _self.hr_id
          }
          let response = await _self.$http.post(apiUrl + 'search_d', param);

          let hrDetail = response.data.info.hrDetail[0];
          _self.hrDetail = JSON.parse(JSON.stringify(hrDetail))
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        await getNotice()
      }

      $(async function() {
        await initPage()
      });
    },
    methods: {
    }
  }
</script>
<style scoped>
</style>
