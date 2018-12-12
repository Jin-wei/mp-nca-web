<template>
    <div class="invoice">
        <div class="invoice-company">
            <span class="pull-right hidden-print">
                <a href="javascript:history.back();" class="btn btn-danger"><i class="fa fa-close"></i> 关闭</a>
                <a href="javascript:;" onclick="window.print()" class="btn btn-success"><i class="fa fa-print"></i> 打印</a>
            </span>
        </div>

        <br>
        <h3 style="text-align: center">物料清单</h3>
        <table class="table table-bordered" v-for="parentItem in materialList">
            <caption>
                <h4 style="text-align: center">{{parentItem.name}}</h4>
            </caption>
            <thead>
            <tr>
                <th class="align-center">物料编码</th>
                <th class="align-center">物料名称</th>
                <th class="align-center">采购来源</th>
                <th class="align-center">规格型号</th>
                <th class="align-center">分类</th>
                <th class="align-center">单位</th>
                <th class="align-center">数量</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in parentItem.data.rows">
                <td class="align-center">{{item.materiel_code}}</td>
                <td class="align-left">{{item.materiel_name}}</td>
                <td class="align-center">{{item.materiel_source|materielResource}}</td>
                <td class="align-center">{{item.materiel_format}}</td>
                <td class="align-center">{{item.materiel_type|materielType}}</td>
                <td class="align-center">{{item.materiel_unit|materielUnit}}</td>
                <td class="align-right">{{item.materiel_amount}}</td>
            </tr>
            </tbody>
        </table>

        <br>
        <div class="invoice-label">
            <div>
                <span>审核意见：</span>
                <span class="right-input">审核人：</span>
            </div>
            <br>
            <br>
            <span class="pull-right">年&emsp;&emsp;&emsp;&emsp;月&emsp;&emsp;&emsp;&emsp;日</span>
        </div>
    </div>
</template>
<script>
import Constant from '../../../../assets/js/constant'
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            queryData: {},
            materialList: []
        }
    },
    name: 'NCAMaterialInvoiceControl',
    filters: {
        materielResource: function (value) {
            let materielSourceInfo = Constant.getData('materielSourceInfo');
            for (let i = 0; i < materielSourceInfo.length; i++) {
                if (materielSourceInfo[i].id == value) {
                    return materielSourceInfo[i].text;
                }
            }
        },
        materielType: function (value) {
            let materialTypeInfo = Constant.getData('materialTypeInfo');
            for (let i = 0; i < materialTypeInfo.length; i++) {
                if (materialTypeInfo[i].id == value) {
                    return materialTypeInfo[i].text;
                }
            }
        },
        materielUnit: function (value) {
            let unitInfo = Constant.getData('unitInfo');
            for (let i = 0; i < unitInfo.length; i++) {
                if (unitInfo[i].id == value) {
                    return unitInfo[i].text;
                }
            }
        }
    },
    methods: {
        initTable: function () {
            let roomIds = [];
            let roomNames = [];
            for (let i = 0; i < this.queryData.roomsInfo.length; i++) {
                roomIds.push(this.queryData.roomsInfo[i].room_id);
                roomNames.push(this.queryData.roomsInfo[i].room_name);
            }

            this.$http.post(apiUrl + 'MaterielCrmControlSRV_searchArray', {
                order_id: this.queryData.order_id,
                room_ids: roomIds,
                room_names: roomNames
            }).then(response => {
                if (response.data.errno === 0) {
                    this.materialList = response.data.info;
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
        this.initTable();
    },
    mounted: function() {

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
    .align-center{
        text-align: center!important;
    }
    .align-left{
        text-align: left!important;
    }
    .align-right{
        text-align: right!important;
    }
</style>
