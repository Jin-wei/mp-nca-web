<template>
<div>
    <div>
        <div class="row">
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
            <div class="row">
                <div class="col-xs-12">
                    <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                        <button type="button" class="btn btn-white" v-for="room in pagePara.roomIdInfo" v-bind:class="{active: selectRoomType === room.id}" @click="roomTypeSwitch(room.id)">{{room.text}}</button>
                    </div>
                    <table id="acceptancePlanTable"></table>
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

    <div class="modal fade" id="checkModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">验收审核</h4>
                </div>
                <div class="modal-body">
                    <div class="form-horizontal">
                        <div class="form-group">
                            <label class="col-md-2 control-label">审核意见</label>
                            <div class="col-md-10">
                                <textarea class="form-control" v-model="sendData.check_message" rows="5"></textarea>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info-reject" v-on:click="acceptanceCheck('1', $event)">驳回</button>
                    <button type="button" class="btn btn-info" v-on:click="acceptanceCheck('2', $event)">通过</button>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const orderDetailApiUrl = '/api/nca/ordermanage/NCAGOrderDetailControl?method=';

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
    name: 'NCAGAcceptanceListControl',
    mounted: function() {
        let _self = this

    },
    watch: {
        order: function() {
            let _self = this
            let $table = $('#acceptancePlanTable');

            function operateFormatter(value, row, index) {
                if (row.acceptance_state === 'CHECKING' && _self.userinfo.user_id === _self.order.order_supervision) {
                    return [
                        '<a class="btn btn-primary btn-xs m-r-5 detail" style="cursor: pointer">',
                        '查看</i>',
                        '</a>',
                        '<a class="btn btn-primary btn-xs m-r-5 check" style="cursor: pointer">',
                        '验收</i>',
                        '</a>'
                    ].join('')
                } else {
                    return [
                        '<a class="btn btn-primary btn-xs m-r-5 detail" style="cursor: pointer">',
                        '查看</i>',
                        '</a>'
                    ].join('')
                }
            }

            function nodeTypeFilter(value, row) {
                for (let i = 0; i < _self.pagePara.nodesInfo.length; i++) {
                    if (_self.pagePara.nodesInfo[i].id == value) {
                        return _self.pagePara.nodesInfo[i].text
                    }
                }
                return ''
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
                for (let i = 0; i < _self.pagePara.tfInfo.length; i++) {
                    if (_self.pagePara.tfInfo[i].id == value) {
                        return _self.pagePara.tfInfo[i].text
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
                        if (_self.detailData.acceptance_state != 'NEW') {
                            $('#submitMessage').modal('show')
                            window.setTimeout(function() {
                                _self.initSwiper()
                            }, 300)
                        } else {
                            common.dealPromptCommon('验收项尚未提交')
                        }

                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                },
                'click .check': function(e, value, row, index) {
                    _self.selectRow = row
                    $('#checkModal').modal('show')
                }
            };

            function queryParams(params) {
                params.order_id = _self.order.order_id;
                if ($('#node_id').val()) {
                    params.node_id = $('#node_id').val()
                }
                if (_self.selectRoomType) {
                    params.room_id = _self.selectRoomType
                }
                return JSON.stringify(params);
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: orderDetailApiUrl + 'search_acceptance',
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
                        common.BTRowFormatWithFormatterAlignLeft('node_id', '施工工序', nodeTypeFilter),
                        common.BTRowFormatWithFormatter('room_type', '空间类型', roomTypeFilter),
                        common.BTRowFormat('acceptance_name', '验收项'),
                        common.BTRowFormatWithFormatter('is_hidden', '隐蔽', trueFalseTypeFilter),
                        common.BTRowFormatWrap('technological_require', '工艺要求'),
                        common.BTRowFormat('acceptance_submit_date_format', '提交时间'),
                        common.BTRowFormat('acceptance_submit_name', '提交人'),
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
                    onEditableSave: function(field, row, oldValue, $el) {}
                })
                common.changeTableClass($table)
            }

            function initPage() {
                let params = {};
                params.template_id = _self.order.template_id
                params.order_id = _self.order.order_id
                params.foreman = _self.order_foreman
                _self.$http.post(orderDetailApiUrl + 'init', params).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData));
                    if (retData.roomTypeInfo.length > 0) {
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
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#AddChangeModal').modal('show')
        },
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        search: function() {
            let _self = this
            $('#acceptancePlanTable').bootstrapTable("refresh", _self.queryParams)
        },
        roomTypeSwitch: function(id) {
            let _self = this
            _self.selectRoomType = id
            $('#acceptancePlanTable').bootstrapTable("refresh", _self.queryParams)
        },
        acceptanceCheck: function(check_state, event) {
            let _self = this
            // act 1 驳回  2 通过
            _self.sendData.order_id = _self.order.order_id
            _self.sendData.orderacceptance_id = _self.selectRow.orderacceptance_id
            _self.sendData.act = check_state
            if (!_self.sendData.check_message && check_state === '1') {
                common.dealPromptCommon('请填写审核意见');
                return
            }
            _self.$http.post(orderDetailApiUrl + 'acceptance_check', _self.sendData).then((response) => {
                let retdata = response.data.info
                $('#checkModal').modal('hide')
                $('#acceptancePlanTable').bootstrapTable("refresh", _self.queryParams)
                _self.sendData = {}
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        initSwiper: function() {
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
