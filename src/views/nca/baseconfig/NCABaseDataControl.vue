<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">运营数据管理</a></li>
            <li class="active">基础数据管理</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">基础数据管理</h4>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active"><a href="#baseCompany" data-toggle="tab"><span>公司管理</span></a></li>
                            <li><a href="#department" data-toggle="tab"><span>部门与岗位</span></a></li>
                            <li><a href="#employee" data-toggle="tab"><span>员工</span></a></li>
                            <li><a href="#materiel" data-toggle="tab"><span>物料</span></a></li>
                            <li><a href="#customer" data-toggle="tab"><span>客户</span></a></li>
                            <li><a href="#supplier" data-toggle="tab"><span>供应商</span></a></li>
                            <li><a href="#account" data-toggle="tab"><span>会计科目</span></a></li>
                            <li><a href="#asset" data-toggle="tab"><span>长期资产</span></a></li>
                            <li><a href="#store" data-toggle="tab"><span>仓储管理</span></a></li>
                            <li><a href="#other" data-toggle="tab"><span>其他</span></a></li>
                        </ul>
                        <div class="tab-content">
                            <!--公司管理1-4-->
                            <div class="tab-pane fade active in" id="baseCompany">
                                <div class="col-sm-6 partDiv">
                                    <h1>主公司基本资料</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(1)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(1)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>主公司后续管理资料</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(2)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>附属公司信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(3)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>银行账号信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(4)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--部门与岗位5-10-->
                            <div class="tab-pane fade" id="department" >
                                <div class="col-sm-6 partDiv">
                                    <h1>部门基本资料</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(5)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(5)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>部门后续管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(6)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>岗位基本资料</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(7)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(7)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>岗位后续管理资料</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(8)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>岗位责任资产信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(9)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>岗位配备的消耗性物料信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(10)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--员工11-12-->
                            <div class="tab-pane fade" id="employee">
                                <div class="col-sm-6 partDiv">
                                    <h1>员工基本信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(11)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(11)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>员工后续管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(12)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--物料13-15-->
                            <div class="tab-pane fade" id="materiel">
                                <div class="col-sm-6 partDiv">
                                    <h1>物料基本信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(13)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(13)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>物料供应商信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(14)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>物料生产信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(15)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--客户16-18-->
                            <div class="tab-pane fade" id="customer">
                                <div class="col-sm-6 partDiv">
                                    <h1>客户基本信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(16)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>客户单价管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(17)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>客户订单信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(18)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--供应商19-21-->
                            <div class="tab-pane fade" id="supplier">
                                <div class="col-sm-6 partDiv">
                                    <h1>供应商基本信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(19)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>供应商价格管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(20)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>采购订单信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(21)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--会计科目22-->
                            <div class="tab-pane fade" id="account">
                                <div class="col-sm-6 partDiv">
                                    <h1>会计明细科目余额、发生额</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(22)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--长期资产23-33-->
                            <div class="tab-pane fade" id="asset">
                                <div class="col-sm-6 partDiv">
                                    <h1>固定资产明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(23)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(23)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>固定资产供应商信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(24)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>固定资产后续管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(25)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>待摊资产明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(26)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(26)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>待摊资产供应商信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(27)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>待摊资产后续管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(28)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>研发资产明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(41)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(41)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>主生产设备明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(29)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>辅助生产设备明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(30)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>低值易耗品明细信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(31)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(31)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>低值易耗品供应商信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(32)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>低值易耗品后续管理信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(33)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--仓储管理38-40-->
                            <div class="tab-pane fade" id="store">
                                <div class="col-sm-6 partDiv">
                                    <h1>仓库信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(38)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(38)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>仓区信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(39)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(39)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>实时库存信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click="importData(40)"><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(40)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                            <!--其他34-37-->
                            <div class="tab-pane fade" id="other">
                                <div class="col-sm-6 partDiv">
                                    <h1>生产工序信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(34)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>会议室信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(35)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>假期信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(36)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                                <div class="col-sm-6 partDiv">
                                    <h1>小食堂信息</h1>
                                    <div class="placeDiv"></div>
                                    <button class="btn btn-info" v-on:click=""><i class="fa fa-upload"></i>上传资料</button>
                                    <button class="btn btn-info" v-on:click="downloadTemp(37)"><i class="fa fa-download"></i>下载模板</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="ImportModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">导入数据</h4>
                    </div>

                    <div class="modal-body">
                        <div class="form-group col-md-12">
                            <!--<label>选择文件：</label><span>（请选.csv文件）</span>-->
                            <div>
                                <input type="file" id="file" class="form-control input-sm " size="1">
                                <input type="text" id="select1" hidden>
                            </div>
                        </div>

                    </div>
                    <div class="modal-footer" style="border-top:none">
                        <button type="button" class="btn btn-primary btn-info" id="importAct" v-on:click="importAct"><i class="fa fa-fw fa-plus"></i>导入</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCABaseDataControl?method=';

    export default {
        data: function() {
            return {
                dataType :''
            }
        },
        name: 'NCABaseDataControl',
        mounted: function() {
            let _self = this;
            $('#file').change(function() {
                let files = this.files;
                let formData = new FormData();
                if(files.length>0){
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.fileUrl = response.data.info.uploadurl.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });

            $(function() {

            })
        },
        methods: {
            downloadTemp: function(type) {
                switch (type) {
                    case 5:
                        window.location.href = '/static/baseDataTemplate/NCADepartment.xlsx';
                        break;

                    case 7:
                        window.location.href = '/static/baseDataTemplate/NCAPosition.xlsx';
                        break;

                    case 11:
                        window.location.href = '/static/baseDataTemplate/NCAEmployee.xlsx';
                        break;

                    case 13:
                        window.location.href = '/static/baseDataTemplate/NCAMateriel.xlsx';
                        break;

                    case 23:
                        window.location.href = '/static/baseDataTemplate/NCAFixed.xlsx';
                        break;

                    case 26:
                        window.location.href = '/static/baseDataTemplate/NCAAmortize.xlsx';
                        break;

                    case 31:
                        window.location.href = '/static/baseDataTemplate/NCAConsumables.xlsx';
                        break;

                    case 38:
                        window.location.href = '/static/baseDataTemplate/NCAWearHouse.xlsx';
                        break;

                    case 39:
                        window.location.href = '/static/baseDataTemplate/NCAWearHouseZone.xlsx';
                        break;

                    case 40:
                        window.location.href = '/static/baseDataTemplate/NCAStoreMap.xlsx';
                        break;

                    case 41:
                        window.location.href = '/static/baseDataTemplate/NCADevelop.xlsx';
                        break;
                }
            },
            importData: function(type) {
                this.dataType = type;
                $('#ImportModal').modal('show');
                $('#file').val('');
            },
            importAct: function(event) {
                if (!this.fileUrl) {
                    return common.dealWarningCommon('请选择文件')
                }
                const params = {
                    uploadurl: this.fileUrl,
                    type: this.dataType
                };
                this.$http.post(apiUrl + 'importData', params).then((response) => {
                    const {successNumber, errorNumber} = response.data.info;
                    if ((successNumber || successNumber === 0) && (errorNumber || errorNumber === 0)) {
                        common.dealSuccessCommon(`${successNumber}个成功 ${errorNumber}个失败`);
                    } else {
                        common.dealSuccessCommon('保存成功');
                    }
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });

                $('#ImportModal').modal('hide')
            },
        }
    }
</script>
<style scoped>
    .partDiv {
        height: 150px;text-align:center
    }
    .placeDiv {
        height:15px;
    }
</style>
