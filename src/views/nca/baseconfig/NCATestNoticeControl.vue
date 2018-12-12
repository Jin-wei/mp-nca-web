<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">内部文件</a></li>
        <li class="active">试题详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-12">
                            <div class="input-group ">
                                <span class="input-group-addon">文件条款</span>
                                <div>
                                    <input class="form-control" v-model="clause_title" readonly>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn" style="position:relative">
                        <button style="position: absolute;right:30px;" class="btn btn-info btn-xs" v-show="read_state == 0" @click="addTNC" id="addTNC">提交</button>
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">试题详情</h4>
                </div>

                <div class="panel-body">
                    <table id="TestNoticeTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentNoticeControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            document_id:'',
            apply:{},
            docdetail:{},
            read_state:'',
            answerArray: [],
            clause_title:'',
        }
    },
    name: 'NCATestNoticeControl',
    mounted: function() {
        let _self = this;
        let $TestNoticeTable = $('#TestNoticeTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            _self.answerArray = _self.pagePara.answerType;
            _self.clause_title =_self.$route.query.clause_title;

            let params ={
                document_id:common.getUrlParams('document_id'),
                docdetail_id:_self.$route.query.docdetail_id,
                user_id: _self.pagePara.userInfo.user_id
            }
            response = await _self.$http.post(apiUrl + 'search_d', params);
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.docdetail =_self.apply.docdetail;
            _self.docuser =_self.apply.docuser;
            _self.read_state = _self.docuser.read_state;
            initAllDemandTable();
            common.reSizeCall();
        }

        function queryParams(params) {
            params.docdetail_id=common.getUrlParams('docdetail_id');
            return JSON.stringify(params)
        }

        function selectFormat(rowid, rowname, dataList, width = 200) {
           return {
               field: rowid,
               title: rowname,
               class: 'text-nowrap',
               align: 'center',
               valign: 'middle',
               editable: {
                   type: 'select2',
                   emptytext: '请选择答案',
                   select2: {
                       data: dataList,
                       width: width
                   },
                   display: function(value) {
                       let showText = '';
                       $(dataList).each(function() {
                           if (value && value !== 'undefined') {
                               if (this.id == value) {
                                   showText = this.text;
                                   return false
                               }
                           }
                       });
                       $(this).html(showText);
                   }
               }
           }
        }

        function initAllDemandTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('question_title', '掌握程度试题'),
                common.BTRowFormat('question_a', '选项A'),
                common.BTRowFormat('question_b', '选项B'),
                common.BTRowFormat('question_c', '选项C'),
                common.BTRowFormat('question_d', '选项D'),
                selectFormat('submit_question_answer', '答案', _self.answerArray)
                // common.BTRowFormatEdSelect2(_self, 'submit_question_answer', '答案', 'answerType'),
            ];

            $TestNoticeTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_q',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'docdetailquestion_id',
                uniqueId: 'docdetailquestion_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function (field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function (field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_q', row, 'docdetailquestion_id', $TestNoticeTable)
                }
            });
            common.changeTableClass($TestNoticeTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        addTNC: function(event) {
            let _self = this;
            let params ={
                document_id:common.getUrlParams('document_id'),
                docdetail_id:_self.$route.query.docdetail_id,
                user_id: _self.pagePara.userInfo.user_id
            }

            this.$http.post(apiUrl + 'submit_sq', params).then((response)=>{
                let retData = response.data.info;
                _self.read_state = retData.read_state;
                console.log('retData',retData)
                common.dealSuccessCommon('已提交');
            }, (error)=>{
                common.dealErrorCommon(this, error)
            });

        }
    }
}
</script>
<style scoped>
</style>
