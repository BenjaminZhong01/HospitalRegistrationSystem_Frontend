<template>
    <div class="app-container">
        <div class="el-toolbar">
            <div class="el-toolbar-body" style="justify-content: flex-start;">
                <el-button type="text" @click="exportData"><i class="fa fa-plus"/> Export</el-button>
                <el-button type="text" @click="importData"><i class="fa fa-plus"/> Import</el-button>
            </div>
        </div>

        <el-table
            :data="list"
            style="width: 100%"
            row-key="id"
            border
            lazy
            :load="getChildrens"
            :tree-props="{children: 'children', hasChildren: 'hasChildren'}">
            <el-table-column label="Name" width="230" align="left">
                <template slot-scope="scope">
                    <span>{{ scope.row.name }}</span>
                </template>
            </el-table-column>

            <el-table-column label="ID" width="220">
                <template slot-scope="{row}">
                    {{ row.dictCode }}
                </template>
            </el-table-column>
            <el-table-column label="Value" width="230" align="left">
                <template slot-scope="scope">
                    <span>{{ scope.row.value }}</span>
                </template>
            </el-table-column>
            <el-table-column label="Created Time" align="center">
                <template slot-scope="scope">
                    <span>{{ scope.row.createTime }}</span>
                </template>
            </el-table-column>
        </el-table>
        <el-dialog title="Import" :visible.sync="dialogImportVisible" width="480px">
            <el-form label-position="right"label-width="170px">
                <el-form-item label="File">
                    <el-upload
                        :multiple="false"
                        :on-success="onUploadSuccess"
                        :action="'http://localhost:8202/admin/cmn/dict/importData'"
                        class="upload-demo">
                        <el-button size="small"type="primary">Upload</el-button>
                        <div slot="tip" class="el-upload__tip">xls file only，no larger than 500kb</div>
                    </el-upload>
                </el-form-item>
            </el-form>
            <div slot="footer"class="dialog-footer">
                <el-button @click="dialogImportVisible = false">cancel</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
import dict from '@/api/dict'
export default {
    data() {
        return {
            list:[], //data dictionary list
            dialogImportVisible: false
        }
    },
    created() {
        this.getDictList(1)
    },
    methods: {
        importData() {
            this.dialogImportVisible = true
        },
        onUploadSuccess() {
            //close window
            this.dialogImportVisible = false
            //refresh page
            this.getDictList(1)
        },
        exportData() {
            window.location.href = "http://localhost:8202/admin/cmn/dict/exportData"
        },
        getDictList(id) {
            dict.dictList(id)
                .then(response => {
                    this.list = response.data
                })
        },
        getChildrens(tree, treeNode, resolve) {
            dict.dictList(tree.id).then(response => {
                resolve(response.data)
            })
        }
    }
}
</script>