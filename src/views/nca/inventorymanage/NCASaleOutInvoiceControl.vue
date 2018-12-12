<template>
    <div class="invoice">
        <div class="invoice-company">
            <span class="pull-right hidden-print">
                <a href="javascript:history.back();" class="btn btn-danger"><i class="fa fa-close"></i> 关闭</a>
                <a href="javascript:;" onclick="window.print()" class="btn btn-success"><i class="fa fa-print"></i> 打印</a>
            </span>
        </div>

        <table class="table table-bordered">
            <caption>
                <h4 style="text-align: center">销售出库单</h4>
                <br>
                <div class="invoice-label">
                    <label>客户名称：{{queryData.name}}</label>
                    <label class="pull-right">NO：{{queryData.bill_code}}</label>
                </div>
            </caption>
            <tbody>
            <tr>
                <th colspan="2">发货地址：</th>
            </tr>
            <tr>
                <th>发货物流公司：</th>
                <th>物流公司联系人：</th>
            </tr>
            <tr>
                <th>物流车号：</th>
                <th>发货司机姓名：</th>
            </tr>
            <tr>
                <th>收货联系人：</th>
                <th>收货联系人电话：</th>
            </tr>
            </tbody>
        </table>
        <table class="table table-bordered">
            <thead>
            <tr>
                <th>物料编码</th>
                <th>物料名称</th>
                <th>规格</th>
                <th>单位</th>
                <th>数量</th>
                <th>备注</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in buyOrderItemList">
                <td>{{item.materiel_code}}</td>
                <td style="text-align: left">{{item.materiel_name}}</td>
                <td>{{item.materiel_format}}</td>
                <td>{{item.materiel_unit|materielFormat}}</td>
                <td style="text-align: right">{{item.account_operate_amount}}</td>
                <td style="text-align: left">{{item.account_note}}</td>
            </tr>
            </tbody>
        </table>
        <div class="invoice-label">
            <div>
                <span>以上货物我方已收到</span>
                <span class="right-input">发货仓库</span>
            </div>
            <div style="margin-top: 10px">
                <span>收货人签名：&emsp;&emsp;&emsp;</span>
                <span class="right-input">发货仓库管理员：</span>
            </div>
            <br>
            <span class="pull-right">年&emsp;&emsp;&emsp;&emsp;月&emsp;&emsp;&emsp;&emsp;日</span>
        </div>
    </div>
</template>
<script>
    import Constant from '../../../assets/js/constant'
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCASaleOutControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                queryData: {},
                buyOrderItemList: []
            }
        },
        name: 'NCASaleOutInvoiceControl',
        filters: {
            materielFormat: function (value) {
                let materielInfo = Constant.getData('materielInfo');
                for (let i = 0; i < materielInfo.length; i++) {
                    if (materielInfo[i].id == value) {
                        return materielInfo[i].text;
                    }
                }
            }
        },
        methods: {
            initTable: function () {
                this.$http.post(apiUrl + 'getSaleOutOrderHistoryDetail', {
                    bill_code: this.queryData.bill_code
                }).then(response => {
                    if (response.data.errno === 0) {
                        this.buyOrderItemList = response.data.info.rows;
                    }
                }, (response) => {
                    console.log('init error');
                    common.dealErrorCommon(this, response)
                });
            }
        },
        created: function () {
            this.queryData = this.$route.query;
            console.log('queryData:', this.queryData);
        },
        mounted: function() {
            this.initTable();
        }
    }
</script>
<style scoped>
    .invoice-label {
        padding: 0 10px;
    }

    .right-input {
        margin-left: 40%;
    }
    .table-bordered thead th{
        text-align: center;
    }
</style>
