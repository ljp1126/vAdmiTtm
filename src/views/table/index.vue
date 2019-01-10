<template>
    <div class="app-container">
        <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" >新增</el-button>
        <el-button  class="filter-item" type="primary" icon="el-icon-download" >导出</el-button>

        <el-table
            v-loading="listLoading"
            :data="list"
            element-loading-text="Loading"
            border
            fit
            highlight-current-row>
            <!-- <el-table-column align="center" label="ID" width="95">
                <template slot-scope="scope">
                {{ scope.$index }}
                </template>
            </el-table-column> -->
            <el-table-column label="Title">
                <template slot-scope="scope">
                {{ scope.row.title }}
                </template>
            </el-table-column>
            <el-table-column label="Author" width="110" align="center">
                <template slot-scope="scope">
                <span>{{ scope.row.author }}</span>
                </template>
            </el-table-column>
            <el-table-column label="Pageviews" width="110" align="center">
                <template slot-scope="scope">
                {{ scope.row.pageviews }}
                </template>
            </el-table-column>
            <!-- <el-table-column class-name="status-col" label="Status" width="110" align="center">
                <template slot-scope="scope">
                <el-tag :type="scope.row.status | statusFilter">{{ scope.row.status }}</el-tag>
                </template>
            </el-table-column> -->
            <el-table-column align="center" prop="created_at" label="Display_time" width="200">
                <template slot-scope="scope">
                <i class="el-icon-time"/>
                <span>{{ scope.row.display_time }}</span>
                </template>
            </el-table-column>
            <el-table-column
                fixed="right"
                label="操作"
                width="100">
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                    <el-button type="text" size="small">编辑</el-button>
                </template>
            </el-table-column>
        </el-table>
  </div>
</template>

<script>
import { getList } from '@/api/table'

export default {
    filters: {
        statusFilter(status) {
        const statusMap = {
            published: 'success',
            draft: 'gray',
            deleted: 'danger'
        }
        return statusMap[status]
        }
    },
    data() {
        return {
        list: null,
        listLoading: true
        }
    },
    created() {
        this.fetchData()
    },
    methods: {
        handleClick(row) {
            console.log(row);
        },
        fetchData() {
        this.listLoading = true
        getList(this.listQuery).then(response => {
            console.log(response)
            this.list = response.data.items
            this.listLoading = false
        })
        }
    }
}
</script>
