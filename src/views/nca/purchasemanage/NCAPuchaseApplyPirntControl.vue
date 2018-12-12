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
                <h4 style="text-align: center">物料采购申请单</h4>
                <br>
                <div class="invoice-label row">
                    <div class="col-sm-4">
                        <label class="pull-right">NO：{{puchaseApply.purchaseapply_id}}</label>
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
            <tr v-for="item in puchaseApply.data">
                <td class="align-center">{{item.materiel_code}}</td>
                <td class="align-left">{{item.materiel_name}}</td>
                <td class="align-center">{{item.materiel_format}}</td>
                <td class="align-center">{{item.materiel_unit|materielUnit}}</td>
                <td class="align-right">{{item.apply_number}}</td>
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
const apiUrl = '/api/nca/purchasemanage/NCAPurchaseApplyDetailControl?method=';
import Constant from '../../../assets/js/constant'
export default {
    data: function() {
        return {
            purchaseapply_id:'',
            puchaseApply:{},
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
        async function getPuchaseApply(){
            _self.purchaseapply_id = common.getUrlParams('purchaseapply_id');
            let response = await _self.$http.post(apiUrl + 'getpuchaseApplyPrint', {purchaseapply_id:_self.purchaseapply_id});
            _self.puchaseApply = response.data.info
        }

        $(function(){
            getPuchaseApply()
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
