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
                <h4 style="text-align: center">采购单</h4>
                <br>
                <div class="invoice-label row">
                    <div class="col-sm-4">
                        <label>采购机构：{{puchaseOrder.puchaseDomainName}}</label>
                    </div>
                    <div class="col-sm-4">
                        <label>销售机构：{{puchaseOrder.orderDomainName}}</label>
                    </div>
                    <div class="col-sm-4">
                        <label class="pull-right">NO：{{puchaseOrder.purchaseorder_id}}</label>
                    </div>
                </div>
            </caption>
            <thead>
            <tr>
                <th class="align-center">物料编码</th>
                <th class="align-center">物料名称</th>
                <th class="align-center">规格</th>
                <th class="align-center">单位</th>
                <th class="align-center">数量</th>
                <th class="align-center">备注</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in puchaseOrder.data">
                <td class="align-center">{{item.materiel_code}}</td>
                <td class="align-left">{{item.materiel_name}}</td>
                <td class="align-center">{{item.materiel_format}}</td>
                <td class="align-center">{{item.materiel_unit|materielUnit}}</td>
                <td class="align-right">{{item.purchase_number}}</td>
                <td></td>
            </tr>
            </tbody>
        </table>
        <div class="invoice-label">
            <span class="pull-right">年&emsp;&emsp;&emsp;&emsp;月&emsp;&emsp;&emsp;&emsp;日</span>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/purchasemanage/NCAPurchaseDetailControl?method=';
import Constant from '../../../assets/js/constant'
export default {
    data: function() {
        return {
            purchaseorder_id:'',
            puchaseOrder:{},
            pagePara:{}
        }
    },
    name: 'NCAPuchasePirntControl',
    filters: {
        materielUnit: function (value) {
            let materielInfo = Constant.getData('materielInfo');
            for (let i = 0; i < materielInfo.unitInfo.length; i++) {
                if (materielInfo.unitInfo[i].id == value) {
                    return materielInfo.unitInfo[i].text;
                }
            }
        }
    },
    mounted:function(){
        let _self = this;
        async function getPuchaseOrder(){
            _self.purchaseorder_id = common.getUrlParams('purchaseorder_id');
            let response = await _self.$http.post(apiUrl + 'getpuchaseOrderPrint', {purchaseorder_id:_self.purchaseorder_id});
            _self.puchaseOrder = response.data.info
        }

        $(function(){
            getPuchaseOrder()
        })

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
