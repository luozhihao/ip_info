<template>
    <modal :show.sync="idcModal" effect="fade" width="550px">
        <div slot="modal-header" class="modal-header">
            <h4 class="modal-title">节点网络</h4>
        </div>
        <div slot="modal-body" class="modal-body">
            <table class="table table-hover table-bordered">
                <thead>
                    <tr>
                        <th>测试节点</th>
                        <th>响应状态</th>
                        <th>响应时间(ms)</th>
                        <th>下载速率(KB/s)</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="list in tableList" v-if="tableList.length">
                        <td v-text="list.node"></td>
                        <td v-text="list.status"></td>
                        <td v-text="list.time"></td>
                        <td v-text="parseFloat(list.rate).toFixed(2)"></td>
                    </tr>
                    <tr v-if="!tableList.length">
                        <td class="text-center" colspan="4">
                            暂无数据
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div slot="modal-footer" class="modal-footer">
            <button type="button" class="btn btn-default" @click='idcModal = false'>关闭</button>
        </div>
    </modal>
</template>

<script>
import { modal } from 'vue-strap'

let origin = {
        idcModal: false,
        tableList: []
    },
    init = Object.assign({}, origin)

export default {
    data () {
        return origin
    },
    components: {
        modal
    },
    events: {
        'showIDC' (param) {
            this.$http({
                url: '/get_reponse_infos/',
                method: 'POST',
                data: {
                    id: param
                }
            })
            .then(response => {
                if (response.data.result === 1) {
                    this.tableList = response.data.data
                    this.idcModal = true
                }
            })
        }
    },
}
</script>

<style>
</style>