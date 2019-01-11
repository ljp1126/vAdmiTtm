<template>
    <div class="app-container">
        <el-button class="filter-item" style="margin-left: 10px;margin-bottom: 10px;" type="primary" icon="el-icon-edit" @click="dialogAdd = true" >新增</el-button>
        <el-button  class="filter-item" type="primary" icon="el-icon-download" @click="dialogUpload = true">导入</el-button>

        <el-table v-loading="listLoading"  :data="list" element-loading-text="Loading" border fit highlight-current-row>
            <!-- <el-table-column align="center" label="ID" width="95">
                <template slot-scope="scope">
                {{ scope.$index }}
                </template>
            </el-table-column> -->
            <el-table-column label="Title" align="center">
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
            <el-table-column fixed="right" label="操作" width="100">
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small">编辑</el-button>
                    <el-button @click="dialogDelete = true" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <pagination v-show="total>0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="fetchData" />
        <el-dialog title="删除" :visible.sync="dialogDelete"  width="30%" :before-close="handleClose">
            <span>确定删除这段信息</span>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogDelete = false">取 消</el-button>
                <el-button type="primary" @click="dialogDelete = false">确 定</el-button>
            </span>
        </el-dialog>
        <el-dialog title="上传文件" :visible.sync="dialogUpload"  width="30%" :before-close="handleClose">
            <el-upload
                class="upload-demo"
                drag
                action="https://jsonplaceholder.typicode.com/posts/"
                multiple style="margin-left:25px;">
                <i class="el-icon-upload"></i>
                <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
                <!-- <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div> -->
            </el-upload>
            <!-- <upload-excel-component :on-success="handleSuccess" :before-upload="beforeUpload"/>
            <el-table :data="tableData" border highlight-current-row style="width: 100%;margin-top:20px;">
                <el-table-column v-for="item of tableHeader" :prop="item" :label="item" :key="item"/>
            </el-table>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogUpload = false">取 消</el-button>
                <el-button type="primary" @click="dialogUpload = false">确 定</el-button>
            </span> -->
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogUpload = false">取消</el-button>
                <el-button type="primary" @click="dialogUpload = false">确定</el-button>
            </span>
        </el-dialog>
        <el-dialog title="新增" :visible.sync="dialogAdd"  width="35%" :before-close="handleClose">
            <!-- <span>编辑这段信息</span> -->
            <el-form  label-position="left" label-width="80px" >
                <el-form-item label="活动名称:">
                    <el-input v-model="listDetial.author"></el-input>
                </el-form-item>
                <el-form-item label="日期时间:">
                    <el-date-picker
                        v-model="value6"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        style="width: 100%;">
                    </el-date-picker>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogAdd = false">取 消</el-button>
                <el-button type="primary" @click="dialogAdd = false">确 定</el-button>
            </span>
        </el-dialog>
        <el-dialog title="编辑" :visible.sync="dialogEdit"  width="35%" :before-close="handleClose">
            <!-- <span>编辑这段信息</span> -->
            <el-form  label-position="left" label-width="80px" >
                <el-form-item label="活动名称:">
                    <el-input v-model="listDetial.author"></el-input>
                </el-form-item>
                <el-form-item label="日期时间:">
                    <el-date-picker
                        v-model="value6"
                        type="daterange"
                        range-separator="至"
                        start-placeholder="开始日期"
                        end-placeholder="结束日期"
                        style="width: 100%;">
                    </el-date-picker>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogEdit = false">取 消</el-button>
                <el-button type="primary" @click="dialogEdit = false">确 定</el-button>
            </span>
        </el-dialog>
  </div>
</template>

<script>
import { getList } from '@/api/table'
import Pagination from '@/components/Pagination'
import UploadExcelComponent from '@/components/UploadExcel/index.vue'

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
     name: 'UploadExcel',
    components: { Pagination, UploadExcelComponent },
    data() {
        return {
            list: null,
            listDetial:'',
            total: 0,
            listLoading: true,
            listQuery: {
                page: 1,
                limit: 10,
                importance: undefined,
                title: undefined,
                type: undefined,
                sort: '+id'
            },
            dialogDelete: false, //删除
            dialogEdit: false, //编辑
            dialogAdd: false, //新增
            dialogUpload: false, //上传
            value6: '',
            tableData: [],
            tableHeader: []
        }
    },
    created() {
        this.fetchData()
    },
    methods: {
        handleClick(row) {
            console.log(row);
            this.listDetial=row
            this.dialogEdit = true
        },
        handleClose(done) {
            this.dialogDelete = false
            this.dialogEdit = false
            this.dialogAdd = false
            this.dialogUpload = false
        },
        fetchData() {
            console.log(this.listQuery)
            this.listLoading = true
            getList(this.listQuery).then(response => {
                console.log(response)
                this.list = response.data.items
                this.total = response.data.items.length
                this.listLoading = false
            })
        },
        beforeUpload(file) {
            const isLt5M = file.size / 1024 / 1024 < 5

            if (isLt5M) {
                return true
            }

            this.$message({
                message: 'Please do not upload files larger than 1m in size.',
                type: 'warning'
            })
            return false
        },
        handleSuccess({ results, header }) {
            this.tableData = results
            this.tableHeader = header
        }
    }
}
</script>
