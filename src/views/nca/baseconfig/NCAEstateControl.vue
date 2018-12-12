<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">总部系统</a></li>
        <li class="active">楼盘管理</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'楼盘列表'"></DomainChooseHeader>
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
                            <div class="form-group">
                                <button id="search" class="btn btn-info margin_right" v-on:click="queryConfirm">
                                      <i class="fa fa-search"></i>
                                  </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">增加
                              </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="table"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">{{title}}</h4>
                </div>

                <div class="modal-body row">
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>城市</label>
                        <div class="col-sm-10">
                            <div class="form-control mp-form-control">
                                <input id="citypicker" style="width: 419px" data-parsley-required="true">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>名称</label>
                        <div class="col-sm-10">
                            <select class="form-control" style="width: 100%" id="estate_select"></select>
                            <!-- <input class="form-control" v-model="workRow.estate_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50"> -->
                        </div>
                    </div>
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>地址</label>
                        <div class="col-sm-10">
                            <input class="form-control" v-model="workRow.address" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                        </div>
                    </div>
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>地产商</label>
                        <div class="col-sm-10">
                            <select class="form-control select2" id="land_agent" data-parsley-required="true"></select>
                        </div>
                    </div>
                </div>
                <div class="modal-footer" style="border-top:none">
                    <button type="button" class="btn btn-primary btn-info" id="addAct" v-on:click="addAct">保存</button>
                </div>
            </div>
        </div>
    </div>

</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEstateControl?method=';
import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
export default {
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara: {},
            estateId: 0,
            estateCount: 0,
            domain_type: 0,
            title: "",
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            masterTable: null,
            listTitle: ''
        }
    },
    name: 'NCAEstateControl',
    components: {
        DomainChooseHeader
    },
    mounted: function() {
        this.masterTable = $('#table');
        $('#citypicker').citypicker()
        $('#distpicker').distpicker({
            placeholder: true
        });

        $('#estate_select').select2({
            ajax: {
                url: '/api/openapi/kujiale',
                dataType: 'json',
                // Additional AJAX parameters go here; see the end of this chapter for the full code of this example
                data: function(params) {
                    let query = {
                        method: 'queryEstate'
                    }
                    let city = $('#citypicker').data('citypicker').getVal()
                    let cityInfo = common.getCityInfo(city)
                    if (cityInfo) {
                        query.province = cityInfo[0]
                        query.city = cityInfo[1]
                        query.district = cityInfo[2]
                        query.search_text = params.term
                    }
                    // Query parameters will be ?search=[term]&type=public
                    return query;
                }
            },
            tags: true,
            language: 'zh-CN'
        });

        $('#estate_select').on('select2:opening', function(e) {
            let city = $('#citypicker').data('citypicker').getVal()
            let cityInfo = common.getCityInfo(city)
            if (!cityInfo) {
                common.dealWarningCommon('请选择城市')
            }
        });

        this.initData();
    },
    methods: {
        timeFormatter: function(value) {
            if (!value) return '';
            let date = value;
            date = date.split('T');
            let time = date[1].slice(0, 8);
            return date[0]
        },
        getDomainType: function() {
            let userinfo = common.getStoreData('userinfo');
            this.domain_type = userinfo.domain_type
        },
        nameFormatter: function(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看房间详情">',
                value,
                '</a>'
            ].join('')
        },
        actFormatter: function() {
            return [
                '<a class="btn btn-info btn-xs m-r-5 update_estate" v-if="domain_type=0">编辑</a>'
                //                    '<a class="btn btn-primary btn-xs m-r-5 delete_estate btn-info-delete">删除</a>'

            ].join('')
        },
        queryParams: function(params) {
            params.source = $('#source').val();
            params.idName = $('#search_text').val();
            params.province = $('#province').val();
            params.city = $('#city').val();
            params.district = $('#district').val();
            //            params.createdBeginTime = $('#start_date').val();
            //            params.createdEndTime = $('#end_date').val();
            return JSON.stringify(params)
        },
        initTable: function() {
            let _self = this;
            window.tableEvents = {
                'click .update_estate': (e, value, row, index) => {
                    $('#citypicker').citypicker('destroy');
                    $('#citypicker').citypicker({
                        province: row.province,
                        city: row.city,
                        district: row.district
                    });
                    _self.workRow = row;
                    $('#estate_select').html('')
                    $('#estate_select').append('<option value="initial" selected="selected">' + row.estate_name + '</option>');
                    $('#estate_select').trigger('change');
                    $('#land_agent').val(row.land_agent).trigger('change');
                    this.estateId = row.estate_id;
                    this.title = '编辑楼盘'
                    $('#AddModal').modal('show')
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
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('estate_no', '楼盘ID', this.nameFormatter, tableEvents),
                common.BTRowFormat('province', '省份'),
                common.BTRowFormat('city', '市县'),
                common.BTRowFormat('district', '区镇'),
                common.BTRowFormatEditableLeft('estate_name', '楼盘名称'),
                //                common.BTRowFormat('landagent_name', '地产商'),
                common.BTRowFormatEdSelectAlignLeft(_self, 'land_agent', '地产商', 'landAgents'),
                common.BTRowFormatEdTextareaWidthAlignLeft('address', '楼盘地址',150,10),
                common.BTRowFormatWithFormatter('created_at', '创建时间', this.timeFormatter),
                common.actFormatter('act', this.actFormatter, tableEvents)
            ];


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
                onEditableSave: async function(field, row, oldValue, $el) {
                    let response = await _self.$http.post(apiUrl + 'modify', row);
                }
            });
            common.changeTableClass(this.masterTable)
        },
        initData: async function() {
            let response = await this.$http.post(apiUrl + 'init', {});
            this.pagePara = response.data.info;
            common.initSelect2($('#land_agent'), this.pagePara.landAgents);
            this.getDomainType();
            this.initTable();
            common.reSizeCall();
        },
        addM: function(event) {
            $('#citypicker').citypicker('reset');
            $('#estate_select').html('')
            this.workRow = {};
            $('#land_agent').val(null).trigger('change');
            this.estateId = 0;
            this.title = '新增楼盘';
            $('#AddModal').modal('show')
        },
        addAct: function(event) {
            let city = $('#citypicker').data('citypicker').getVal()
            let cityInfo = common.getCityInfo(city)
            if (!cityInfo) {
                return common.dealWarningCommon('请选择城市')
            }
            // if (!this.workRow.address) {
            //     return common.dealWarningCommon('请填写地址')
            // }
            this.workRow.estate_name = $('#estate_select').select2("data")[0].text
            if (!this.workRow.estate_name) {
                return common.dealWarningCommon('请填写楼盘名称')
            }
            this.workRow.province = cityInfo[0];
            this.workRow.city = cityInfo[1];
            this.workRow.district = cityInfo[2];
            this.workRow.land_agent = common.getSelect2Val('land_agent');
            this.workRow.estate_id = this.estateId;
            if (this.estateId == 0) {
                this.$http.post(apiUrl + 'add', this.workRow).then(response => {
                    let retData = response.data.info;
                    this.masterTable.bootstrapTable("refresh", this.queryParams);
                    this.workRow = {};
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(this, response)
                })
            } else {
                this.$http.post(apiUrl + 'modify', this.workRow).then(response => {
                    let retData = response.data.info;
                    this.masterTable.bootstrapTable("refresh", this.queryParams);
                    this.workRow = {};
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(this, response)
                })
            }
            $('#AddModal').modal('hide')
        },
        queryConfirm: function(event) {
            function queryParams(params) {
                params.idName = $('#search_text').val();
                params.province = $('#province').val();
                params.city = $('#city').val();
                params.district = $('#district').val();
                //                params.createdBeginTime = $('#start_date').val();
                //                params.createdEndTime = $('#end_date').val();
                return JSON.stringify(params)
            }

            this.masterTable.bootstrapTable("refresh")
        },
        examine: function(event) {
            $('#examineModal').modal('show')
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

.select-width {
    width: 110px;
}

#distpicker {
    padding-right: 20px;
}

#start_date {
    width: 100px;
}

#end_date {
    width: 100px;
}

#examineModal-content {
    width: 1200px;
    left: -250px
}

.font-style {
    font-family: 'Open Sans', 'Helvetica Neue', Helvetica, Arial, sans-serif;
    font-size: 12px;
    color: #242a30;
    font-weight: 600;
    display: inline-block;
    max-width: 100%;
}

.p_r {
    position: relative;
}

.p_a {
    position: absolute;
    left: 57px;
    top: 2px;
}

.m_l {
    margin-left: -21px;
}

.margin_right {
    margin-left: -19px;
}
</style>
