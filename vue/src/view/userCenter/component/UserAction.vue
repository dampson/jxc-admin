<template>
    <div v-loading="config.loading">
        <abstract-table :data="tableData" :highlight-current-row="false">
            <el-table-column align="center" label="#" type="index" width="60">
                <el-button icon="el-icon-refresh-right" slot="header" style="padding: 0" type="text" @click="search"/>
            </el-table-column>
            <el-table-column align="center" label="ip" prop="ip" show-overflow-tooltip/>
            <el-table-column align="center" label="操作" prop="action" show-overflow-tooltip/>
            <el-table-column align="center" label="时间" show-overflow-tooltip>
                <template v-slot="{row}">{{ row.time | timestamp2Date }}</template>
            </el-table-column>
            <el-table-column align="center" label="状态" width="80">
                <template v-slot="{row}">
                    <el-tooltip :disabled="row.success" placement="left">
                        <div slot="content" style="max-width: 60vw">
                            <el-scrollbar wrap-class="el-select-dropdown__wrap">
                                <p>{{ row.error }}</p>
                            </el-scrollbar>
                        </div>
                        <el-tag :type="row.success ? 'success' : 'danger'" size="small" effect="dark">
                            {{ row.success ? '成功' : '失败' }}
                        </el-tag>
                    </el-tooltip>
                </template>
            </el-table-column>
        </abstract-table>

        <abstract-pagination :model="searchForm" @current-change="pageChange"/>
    </div>
</template>

<script>
import tablePageMixin from '@/mixin/tablePageMixin'
import {searchUserAction} from "@/api/record"

export default {
    name: "UserAction",

    mixins: [tablePageMixin],

    data() {
        return {
            searchForm: {
                pageSize: 10,
            }
        }
    },

    methods: {
        search() {
            if (this.config.loading) return
            this.config.loading = true
            searchUserAction
                .request({...this.searchForm, uid: this.$store.state.user.id})
                .then(({data: {list, total}}) => {
                    this.searchForm.total = total
                    this.tableData = list
                })
                .finally(() => this.config.loading = false)
        }
    }
}
</script>
