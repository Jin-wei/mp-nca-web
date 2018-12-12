<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li class="active">地产商楼盘管理</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">地产商楼盘列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group" id="search_div">
                                <input class="form-control" multiple v-model="workRow.search_text" id="search_text" placeholder="搜索ID、名称 ">
                            </div>

                            <div data-toggle="distpicker" id="distpicker" class="form-group">
                                <div class="form-group">
                                    <select class="form-control picker-width" id="province"></select>
                                </div>
                                <div class="form-group">
                                    <select class="form-control picker-width" id="city"></select>
                                </div>
                                <div class="form-group">
                                    <select class="form-control picker-width" id="district"></select>
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
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
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCALandAgentEstateControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            pagePara: {},
            userGroup: {},
            landAgent: {},
            estateId: 0,
            estateCount: 0,
            domain_type: 0,
            title: "",
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            masterTable: null
        }
    },
    name: 'NCALandAgentEstateControl',
    mounted: function() {
        this.masterTable = $('#table');

        $('#distpicker').distpicker({
            placeholder: true
        });
        $('#distpickerAdd').distpicker({
            placeholder: true
        });
        this.initData();
    },
    methods: {
        timeFormatter: function (value) {
            if (!value) return '';
            let date = value;
            date = date.split('T');
            let time = date[1].slice(0, 8);
            return date[0]
        },
        getDomainType: function () {
            let userinfo = common.getStoreData('userinfo');
            this.domain_type = userinfo.domain_type
        },
        nameFormatter: function (value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看房间详情">',
                value,
                '</a>'
            ].join('')
        },
        queryParams: function (params) {
            params.idName = $('#search_text').val();
            params.province = $('#province').val();
            params.city = $('#city').val();
            params.district = $('#district').val();
            params.land_agent = this.landAgent.landagent_id;
            return JSON.stringify(params)
        },
        initTable: function () {
            window.tableEvents = {
                'click .update_estate': (e, value, row, index) => {
                    $('#distpickerAdd').distpicker('reset', true);
                    $('#distpickerAdd').distpicker('destroy', true);
                    $('#distpickerAdd').distpicker({
                        province: row.province,
                        city: row.city,
                        district: row.district
                    });
                    this.workRow = row;
                    $('#land_agent').val(row.land_agent).trigger('change');
                    this.estateId = row.estate_id;
                    this.title = '编辑楼盘'
                },
                'click .delete_estate': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '确认删除该楼盘？', apiUrl + 'delete', this.masterTable, row, 'estate_id')
                },
                'click .show_detail': (e, value, row, index) => {
                    this.$router.push({
                        path: '/nca/baseconfig/NCAEstateDetailControl',
                        query: {
                            estateId: row.estate_id
                        }
                    })
                }
            };
            let columsArr = [];
            if (this.domain_type == 1) {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('estate_no', '楼盘ID', this.nameFormatter, tableEvents),
                    common.BTRowFormat('province', '省份'),
                    common.BTRowFormat('city', '市县'),
                    common.BTRowFormat('district', '区镇'),
                    common.BTRowFormatAlignLeft('estate_name', '楼盘名称'),
                    common.BTRowFormatWithFormatter('created_at', '创建时间', this.timeFormatter)
                ];
            } else {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('estate_no', '楼盘ID', this.nameFormatter, tableEvents),
                    common.BTRowFormat('province', '省份'),
                    common.BTRowFormat('city', '市县'),
                    common.BTRowFormat('district', '区镇'),
                    common.BTRowFormatAlignLeft('estate_name', '楼盘名称'),
                    common.BTRowFormatAlignLeft('landagent_name', '地产商'),
                    common.BTRowFormatWithFormatterAlignLeft('address', '楼盘地址',common.remarkFormatter),
                    common.BTRowFormatWithFormatter('created_at', '创建时间', this.timeFormatter),
                    common.actFormatter('act', () => {
                        return `<a class="btn btn-info btn-xs m-r-5 update_estate btn-info-table" v-if="domain_type=0">编辑</a>`;
                    }, this.tableEvents)
                ];
            }

            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'estate_id',
                uniqueId: 'estate_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass(this.masterTable)
        },
        initData: async function () {
            let resultData = await this.$http.post(apiUrl + 'getLandAgentUserInfo', {});
            console.log('resultData:', resultData.data.info);
            this.userGroup = resultData.data.info.userGroup;
            this.landAgent = resultData.data.info.landAgent;

            let response = await this.$http.post(apiUrl + 'init', {});
            this.pagePara = response.data.info;
            common.initSelect2($('#land_agent'), this.pagePara.domains);
            this.getDomainType();
            this.initTable();
            common.reSizeCall();
        },
        queryConfirm: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams)
        }
    }
}
</script>
<style scoped>
#search_div {
    width: 200px;
}

#search_div input {
    width: 200px;
}

#distpicker {
    padding-right: 20px;
}
</style>
