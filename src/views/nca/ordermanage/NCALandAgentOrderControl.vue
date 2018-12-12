<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li class="active">地产商订单列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">地产商订单列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" v-model="workRow.search_text" placeholder="搜索订单号、姓名、手机号、地址" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="order_state_group">
                                    <option value="">请选择订单状态</option>
                                    <option v-for="state in pagePara.orderStateGroupInfo" v-bind:value="state.id">{{state.text}}</option>
                                </select>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="search">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="table"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="QRModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">二维码</h4>
                </div>
                <div class="modal-body" v-html="QRHtml">

                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCALandAgentOrderControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            act: 0,
            QRHtml:'',
            userGroup: null,
            landAgent: null,
            estateArray: [],
            masterTable: null
        }
    },
    name: 'NCALandAgentOrderControl',
    mounted: function() {
        this.masterTable = $('#table');
        this.initPage();
    },
    methods: {
        queryParams: function (params) {
            if ($('#order_state_group').val()) {
                params.order_state = $('#order_state_group').val()
            }
            params.search_text = this.workRow.search_text;
            params.landagent_id = this.landAgent.landagent_id;
            return JSON.stringify(params)
        },
        initTable: function () {
            window.tableEvents = {
                'click .show_detail': (e, value, row, index) => {
                    if(row.order_type === '7') {
                        this.$router.push({
                            path: '/nca/ordermanage/NCAGOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }
                },
                'click .qrCode': (e, value, row, index) => {
                    this.$http.post(apiUrl + 'qrcode', {order_id: row.order_id}).then(response => {
                        $('#QRModal').modal('show');
                        this.QRHtml = response.data.info.qrcode;
                    });
                }
            };

            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchForLandAgent',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号', (value, row) => {
                        return `<a class="show_detail" style="cursor: pointer;">${value}</a>`
                    }, tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatWithFormatterAlignLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormat('order_house_area', '面积（平米）'),
                    common.BTRowFormatEdSelect2Disabled(this, 'order_state', '状态', 'orderStateGroupInfo'),
                    common.BTRowFormat('create_date', '创建日期'),
                    common.BTRowFormatWithFE('', '操作', () => {
                        return `<a class="btn btn-info btn-xs m-r-5 qrCode btn-info-delete">查看二维码</a>`;
                    }, tableEvents)
                ],
                idField: 'order_id',
                uniqueId: 'order_id',
                toolbar: '#toolbar',
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass(this.masterTable);
        },
        initPage: async function () {
            let resultData = await this.$http.post(apiUrl + 'getLandAgentUserInfo', {});
            this.userGroup = resultData.data.info.userGroup;
            this.landAgent = resultData.data.info.landAgent;
            this.estateArray = resultData.data.info.estateArray;

            let response = await this.$http.post(apiUrl + 'init', {});
            this.pagePara = response.data.info;
            this.initTable();
            common.reSizeCall();
        },
        search: function() {
            this.masterTable.bootstrapTable("refresh");
        }
    }
}
</script>
<style scoped>
.select-width {
    width: 150px;
}
</style>
