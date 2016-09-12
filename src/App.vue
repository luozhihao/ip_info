<!-- 总组件 -->
<template>
    <div class="view-port">
        <div class="log-box">
            <div class="panel panel-default log-header">
                <div class="panel-heading header-style">
                    <h1 class="panel-title">
                        <ul class="circle-box">
                            <li class="red-circle circle"></li>
                            <li class="yellow-circle circle"></li>
                            <li class="gray-circle circle"></li>
                        </ul>
                        玩家网络收集系统
                        <button class="btn btn-sm btn-default exit-btn" @click="exitFn">
                            <span class="glyphicon glyphicon-off"></span>
                        </button>
                    </h1>
                </div>
                <div class="panel-body">
                    <form class="form-inline mb20">
                        <div class="form-group">
                            <label class="control-label">搜索：</label>
                            <input type="text" class="form-control search-input" v-model="param.search" placeholder="IP/省份/城市/通行证" debounce="500">
                        </div>
                        <div class="form-group">
                            <label class="control-label">时间范围：</label>
                            <input type="text" class="form-control time-input" onfocus="this.blur()" @click="showCalendar" v-model="param.time" placeholder="选择范围">
                            <calendar :show.sync="show" :value.sync="param.time" :x="x" :y="y" :range="range" :type="type"></calendar>
                        </div>
                    </form>
                    <table class="table table-hover table-bordered">
                        <thead>
                            <tr>
                                <th>IP</th>
                                <th>通行证</th>
                                <th>省份</th>
                                <th>城市</th>
                                <th>时间</th>
                                <th>操作</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="list in tableList" v-if="tableList.length">
                                <td v-text="list.ip"></td>
                                <td v-text="list.pass"></td>
                                <td v-text="list.province"></td>
                                <td v-text="list.city"></td>
                                <td v-text="list.created"></td>
                                <td>
                                    <button class="btn btn-sm btn-default" @click="$broadcast('showIDC', list.id)">
                                        <span class="glyphicon glyphicon-search"></span>
                                        查看
                                    </button>
                                </td>
                            </tr>
                            <tr v-if="!tableList.length">
                                <td class="text-center" colspan="6">
                                    暂无数据
                                </td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <tr>
                                <td colspan="6">
                                    <button class="btn btn-default pull-left" @click="refresh">刷新</button>
                                    <boot-page v-ref:page :async="true" :lens="lenArr" :page-len="pageLen" :url="url" :param="param"></boot-page>
                                </td>
                            </tr>
                        </tfoot>
                    </table>
                </div>
            </div>
        </div>
        <idc-modal></idc-modal>
    </div>
</template>

<script>
import bootPage from './BootPage.vue'
import calendar from './Calendar.vue'
import idcModal from './Modal.vue'

export default {
    data () {
        return {
            lenArr: [10, 50, 100],
            pageLen: 5,
            url: '/get_ip_infos/',
            tableList: [],
            param: {
                search: '',
                time: ''
            },
            show: false,
            type: 'date', 
            x: 0,
            y: 0,
            range: true
        }
    },
    methods: {

        // 刷新数据
        refresh () {
            this.$refs.page.refresh()
        },

        // 退出
        exitFn () {
            this.$http({
                url: '/logout/',
                method: 'POST'
            })
            .then(response => {
                location.href='/login/'
            })
        },

        // 显示日期控件
        showCalendar (e) {
            e.stopPropagation();

            var that = this
            that.show = true
            that.x = e.target.offsetLeft
            that.y = e.target.offsetTop + e.target.offsetHeight + 8

            var bindHide = function(e) {
                e.stopPropagation()
                that.show = false
                document.removeEventListener('click', bindHide, false)
            }

            setTimeout(function() {
                document.addEventListener('click', bindHide, false)
            }, 500)
        }
    },
    ready () {

    },
    components: {
        bootPage,
        calendar,
        idcModal
    },
    watch: {
        'param.search' () {
            this.refresh()
        },
        'param.time' () {
            this.refresh()
        }
    },
    events: {

        // 获取表格数据
        'data' (param) {
            this.tableList = param.data
        }
    }
}
</script>

<style>
.log-header .panel-title {
    text-align: center;
    font-size: 18px !important;
}

.log-box {
    width: 80%;
    position: absolute;
    left: 50%;
    top: 10%;
    margin-left: -40%;
}

.exit-btn {
    position: absolute;
    right: 20px;
    top: 7px;
}

.list-box {
    min-height: 300px;
    max-height: 400px;
    overflow: auto;
}

.log-header .panel-heading {
    color: #5F5F5F;
    background-color: #E4E4E4;
}

.circle-box {
    position: absolute;
    left: 13px;
    top: 14px;
    list-style: none;
    padding: 0;
}

.circle {
    width: 14px;
    height: 14px;
    float: left;
    margin-right: 9px;
    border: 1px solid #ccc;
    border-radius: 50%;
}

.red-circle {
    background: #ED544B;
}

.yellow-circle {
    background: #F7BE36;
}

.gray-circle {
    background: #CFCFCF;
}

.search-input {
    width: 215px;
}
</style>