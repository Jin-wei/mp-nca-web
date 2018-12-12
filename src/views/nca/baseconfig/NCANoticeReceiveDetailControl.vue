<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">公告管理</a></li>
            <li class="active">公告详情</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" v-show="receiverInfo.read_state == 0"
                                    @click="clickDone">完成</button>
                            <a class="btn btn-info btn-xs"  href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">公告详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">发布人</span>
                                    <input class="form-control" v-model="noticeDetal.username" data-parsley-required="true" data-parsley-maxlength="60" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">发布时间</span>
                                    <input class="form-control" v-model="noticeDetal.created_at" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">主题</span>
                                    <input class="form-control" v-model="noticeDetal.notice_title" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <div class="input-group ">
                                    <span class="input-group-addon">详情</span>
                                    <div>
                                        <textarea rows="5" class="form-control" style="resize:none;"  v-model="noticeDetal.notice_detail" id="detail" disabled></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row" v-show="receiverInfo.read_state == 0">
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">问题</span>
                                    <input class="form-control" v-model="noticeDetal.notice_question" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">答案</span>
                                    <select class="form-control select2" style="width:100%" id="answer" data-parsley-required="true"></select>
                                    <!--<input class="form-control" id="answer" data-parsley-required="true" data-parsley-maxlength="20">-->
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">A</span>
                                    <input class="form-control" id="notice_answera" v-model="noticeDetal.notice_answera" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">B</span>
                                    <input class="form-control" id="notice_answerb" v-model="noticeDetal.notice_answerb" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">C</span>
                                    <input class="form-control" id="notice_answerc" v-model="noticeDetal.notice_answerc" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">D</span>
                                    <input class="form-control" id="notice_answerd" v-model="noticeDetal.notice_answerd" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row" v-show="sFiles.length != 0">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">附件信息</h4>
                    </div>
                    <div class="panel-body">
                        <table id="noticeFilesTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/baseconfig/NCANoticeControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        notice_id: null,
        noticeDetal: {},
        noticeReceiver: {},
        receiverInfo: {},
        sFiles: {}
      }
    },
    name: 'NCANoticeReceiveDetailControl',
    mounted: function() {
      let _self = this;
      _self.notice_id = _self.$route.query.notice_id
      let $fileTable = $('#noticeFilesTable');

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
              notice_id: _self.notice_id
          }
          let response = await _self.$http.post(apiUrl + 'search_d', param);
          let noticeDetal = response.data.info.noticeDetal[0];
          let noticeReceiver = response.data.info.noticeReceiver[0];
          let receiverInfo = response.data.info.receiverInfo;
          let sFiles = response.data.info.sFiles;

          _self.noticeDetal = JSON.parse(JSON.stringify(noticeDetal))
          _self.receiverInfo = JSON.parse(JSON.stringify(receiverInfo))
          _self.noticeReceiver = JSON.parse(JSON.stringify(noticeReceiver))
          _self.sFiles = JSON.parse(JSON.stringify(sFiles))
          common.initSelect2($('#answer'), _self.pagePara.noticeAnswer)
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }
        function initFileTable() {
            let columns;
            columns = [
                common.BTRowFormat('notice_title', '主题'),
                common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
            ]

            $fileTable.bootstrapTable('destroy');

            $fileTable.bootstrapTable({
                columns: columns,
                idField: 'notice_id',
                uniqueId: 'notice_id',
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

        async function getTablesData() {
            try {
                let response = await _self.$http.post(apiUrl + 'search_files', {
                    notice_id: _self.notice_id
                });
                let retdata = response.data.info;
//                let sFiles = response.data.info.designs[0].files;
//                _self.sFiles = JSON.parse(JSON.stringify(sFiles))
                $('#noticeFilesTable').bootstrapTable('load', {
                    data: retdata.designs
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
      async function initPage() {
        await initFileTable()
        await getInit()
        await getNotice()
        await getTablesData(_self);
      }

      $(async function() {
        await initPage()
      });
    },
    methods: {
        clickDone: function() {
            let _self = this
            let param = {
                notice_id: _self.notice_id,
                answer: $('#answer').val()
            }
            _self.$http.post(apiUrl + 'complete', param).then((response)=>{
                common.dealSuccessCommon('答案已提交');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        }
    }
  }
</script>
<style scoped>
</style>
