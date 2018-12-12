<template>
    <div class="panel panel-inverse">
        <div class="panel-body">
            <div v-show="!checkState('SIGNED')">
                等待订单签约完成
            </div>
            <div v-show="checkState('SIGNED')">
                <div class="col-md-12 no-padding">
                    <div id="">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="node_id">
                                        <option value="">请选择工序</option>
                                        <option v-for="node in pagePara.nodesInfo" v-bind:value="node.id">{{node.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-12 no-padding">
                        <div class="btn-group" style="padding-right: 10px; padding-top: 10px">
                            <button type="button" class="btn btn-white" v-for="room in pagePara.roomIdInfo" v-bind:class="{active: selectRoomType === room.id}" @click="roomTypeSwitch(room.id)">{{room.text}}</button>
                        </div>
                        <div style="padding: 10px 0">
                            <table id="acceptancePlanTable"></table>
                        </div>
                    </div>
                </div>

            </div>

            <div class="modal fade" id="submitMessage" data-backdrop="static">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                            <h5 class="modal-title">验收单:{{detailData.acceptance_name}}</h5>
                        </div>
                        <div class="modal-body">
                            <div class="row">
                                <span>{{detailData.acceptance_submit_date_format}}|{{submitName}}|验收提交</span>
                            </div>
                            <div class="row">
                                <p>{{detailData.acceptance_comment}}</p>
                            </div>
                            <div class="row">
                                <div class="swiper-container">
                                    <div class="gallery-top">
                                        <div class="swiper-wrapper">
                                            <div class="swiper-slide" v-for="pic in detailData.check_pic">
                                                <img class="imgSize" :src="pic">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="gallery-thumbs">
                                        <div class="swiper-wrapper">
                                            <div class="swiper-slide" v-for="pic in detailData.check_pic">
                                                <img class="imgSize" :src="pic">
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div v-show="detailData.acceptance_check_date">
                                <div class="row">
                                    <span v-if="detailData.acceptance_state === 'REJECTED'">{{detailData.acceptance_check_date_format}}|{{checkName}}|验收驳回</span>
                                    <span v-if="detailData.acceptance_state === 'APPROVED'">{{detailData.acceptance_check_date_format}}|{{checkName}}|验收通过</span>
                                </div>
                                <div class="row">
                                    <p v-if="detailData.acceptance_state === 'REJECTED'">{{detailData.rejected_msg[0]}}</p>
                                    <p v-if="detailData.acceptance_state === 'APPROVED'">{{detailData.approve_msg}}</p>
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
  const orderDetailApiUrl = '/api/nca/ordermanage/NCAHDOrderDetailControl?method=';

  export default {
    props: ['order'],
    data: function() {
      return {
        userinfo: common.getStoreData('userinfo'),
        workRow: {},
        oldRow: '',
        pagePara: {},
        selectRoomType: null,
        detailData: {},
        sendData: {},
        selectRow: {},
        submitName: null,
        checkName: null
      }
    },
    name: 'acceptancePlanControl',
    mounted: function() {
      let _self = this

    },
    watch: {
      order: function() {
        let _self = this
        let $table = $('#acceptancePlanTable');
        function operateFormatter(value, row, index) {
            return [
              '<a class="btn btn-primary btn-xs m-r-5 detail" style="cursor: pointer">',
              '查看</i>',
              '</a>'
            ].join('')
        }

        function roomTypeFilter(value, row) {
          for (let i = 0; i < _self.pagePara.roomTypeInfo.length; i++) {
            if (_self.pagePara.roomTypeInfo[i].id == value) {
              return _self.pagePara.roomTypeInfo[i].text
            }
          }
          return ''
        }

        function trueFalseTypeFilter(value, row) {
          for (let i = 0; i < _self.pagePara.trueFalseInfo.length; i++) {
            if (_self.pagePara.trueFalseInfo[i].id == value) {
              return _self.pagePara.trueFalseInfo[i].text
            }
          }
          return ''
        }

        function userNameFilter(value, row) {
          for (let i = 0; i < _self.pagePara.employeeInfo.length; i++) {
            if (_self.pagePara.employeeInfo[i].id == value) {
              return _self.pagePara.employeeInfo[i].text
            }
          }
          return ''
        }

        function convertToUserName(userId) {
          for (let i = 0; i < _self.pagePara.employeeInfo.length; i++) {
            if (_self.pagePara.employeeInfo[i].id == userId) {
              return _self.pagePara.employeeInfo[i].text
            }
          }
          return ''
        }

        function stateTypeFilter(value) {
          for (let i = 0; i < _self.pagePara.stateTypeInfo.length; i++) {
            if (_self.pagePara.stateTypeInfo[i].id === value) {
              return _self.pagePara.stateTypeInfo[i].text
            }
          }
          return ''
        }

        function iconFormatter(value, row, index) {
          let retString = '<div class="form-inline" role="form">'
          if (value === 'APPROVED') {
            retString += '<i class="fa fa-lg fa-check-square" style="color: #0fd16f">'
          } else {
            retString += '<span>' + stateTypeFilter(value) + '<span>'
          }
          retString += '</div>'
          return retString
        }

        window.tableEvents = {
          'click .detail': function(e, value, row, index) {
            let params = {}
            params.orderacceptance_id = row.orderacceptance_id
            _self.$http.post(orderDetailApiUrl + 'acceptance_detail', params).then((response) => {
              let retData = response.data.info
              _self.detailData = JSON.parse(JSON.stringify(retData))
              _self.submitName = convertToUserName(_self.detailData.acceptance_submit_id)
              _self.checkName = convertToUserName(_self.detailData.acceptance_check_id)
              if(_self.detailData.acceptance_state != 'NEW'){
                $('#submitMessage').modal('show')
                window.setTimeout(function () {
                  _self.initSwiper()
                },300)
              }else{
                common.dealPromptCommon('验收项尚未提交')
              }

            }, (response) => {
              common.dealErrorCommon(_self, response)
            })
          }
        };

        function queryParams(params) {
          params.order_id = _self.order.order_id;
          if($('#node_id').val()){
            params.node_id = $('#node_id').val()
          }
          if(_self.selectRoomType){
            params.room_id = _self.selectRoomType
          }
          return JSON.stringify(params);
        }

        function initTable() {
          $table.bootstrapTable({
            method: 'POST',
            url: orderDetailApiUrl + 'acceptance_search',
            queryParams: queryParams,
            sidePagination: 'server',
            ajaxOptions: common.bootstrapTableAjaxOtions,
            responseHandler: function(res) {
              return res.info;
            },
            height: common.getTableHeight(),
            columns: [
              common.BTRowFormatWithIndex('NO.'),
              common.BTRowFormat('acceptance_index', '验收编号'),
              common.BTRowFormatAlignLeft('text', '施工工序'),
              common.BTRowFormatWithFormatter('room_type', '空间类型', roomTypeFilter),
              common.BTRowFormatAlignLeft('acceptance_name', '验收项'),
              common.BTRowFormatWithFormatter('is_hidden', '隐蔽', trueFalseTypeFilter),
              common.BTRowFormatWrap('technological_require', '工艺要求'),
              common.BTRowFormat('acceptance_submit_date_format', '提交时间'),
              common.BTRowFormatWithFormatterAlignLeft('acceptance_submit_id', '提交人',userNameFilter),
              common.BTRowFormatWithFormatter('acceptance_state', '状态', iconFormatter),
              common.BTRowFormat('acceptance_check_date_format', '验收时间'),
              common.actFormatter('acceptanceAct', operateFormatter, tableEvents)
            ],
            idField: 'orderacceptance_id',
            uniqueId: 'orderacceptance_id',
            pagination: true,
            pageSize: common.pageSize(),
            pageList: [10, 15, 25, 50, 100],
            locale: 'zh-CN',
            onEditableShown: function(field, row, $el, editable) {
              _self.oldRow = $.extend(true, {}, row)
            },
            onEditableSave: function(field, row, oldValue, $el) {
            }
          })
          common.changeTableClass($table)
        }

        function initPage() {
          let params = {};
          params.template_id = _self.order.template_id
          params.order_id = _self.order.order_id
          params.foreman = _self.order_foreman
          _self.$http.post(orderDetailApiUrl + 'acceptance_plan_init', params).then((response) => {
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            if(retData.roomTypeInfo.length>0){
              _self.selectRoomType = retData.roomIdInfo[0].id;
            }
            initTable();
            $('#acceptancePlanTable').bootstrapTable("refresh", queryParams)
          }, (response) => {
            common.dealErrorCommon(_self, response)
          })
        }

        $(function() {
          initPage()
        })
      }
    },
    methods: {
      checkState: function(state, event) {
        let _self = this
        if (_self.order.statedict) {
          if (state in _self.order.statedict) return true
        }
        return false
      },
      search: function() {
        let _self = this
        $('#acceptancePlanTable').bootstrapTable("refresh",_self.queryParams)
      },
      roomTypeSwitch: function(id) {
        let _self = this
        _self.selectRoomType = id
        $('#acceptancePlanTable').bootstrapTable("refresh", _self.queryParams)
      },
      initSwiper: function () {
        var top = new Swiper('.gallery-top', {
          spaceBetween: 10
        });

        var thumbs = new Swiper('.gallery-thumbs', {
          spaceBetween: 10,
          centeredSlides: true,
          slidesPerView: 'auto',
          touchRatio: 0.2,
          slideToClickedSlide: true
        });
        top.params.control = thumbs;
        thumbs.params.control = top;
      }
    }
  }
</script>
<style scoped>
    .select-width {
        width: 150px;
    }
    .swiper-container {
        width: 100%;
        height: 500px;
        margin-left: auto;
        margin-right: auto;
    }
    .imgSize {
        width: 100%;
        height: 100%;
    }
    .swiper-slide {
        background-size: cover;
        background-position: center;
    }
    .gallery-top {
        height: 80%;
        width: 100%;
    }
    .gallery-thumbs {
        height: 20%;
        box-sizing: border-box;
        padding: 10px 0;
    }
    .gallery-thumbs .swiper-slide {
        width: 25%;
        height: 100%;
        opacity: 0.4;
    }
    .gallery-thumbs .swiper-slide-active {
        opacity: 1;
    }
</style>
