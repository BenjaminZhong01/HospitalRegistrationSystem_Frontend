<template>
    <div class="app-container">
        <el-form :inline="true" class="demo-form-inline">
            <el-form-item>
                <el-input  v-model="searchObj.hosname" placeholder="Hospital Name"/>
            </el-form-item>
            <el-form-item>
                <el-input v-model="searchObj.hoscode" placeholder="Hospital Index"/>
            </el-form-item>
            <el-button type="primary" icon="el-icon-search" @click="getList()">search</el-button>
        </el-form>

        <!-- batch delete -->
        <div>
            <el-button type="primary" size="mini" @click="removeRows()">Batch Delete</el-button>
        </div>

        <el-table
            :data="list" stripe style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"/>
            <el-table-column type="index" width="50" label="No."/>
            <el-table-column prop="hosname" label="Hospital Name"/>
            <el-table-column prop="hoscode" label="Hospital Index"/>
            <el-table-column prop="apiUrl" label="API Basic Path" width="200"/>
            <el-table-column prop="contactsName" label="Contact Name"/>
            <el-table-column prop="contactsPhone" label="Contact Phone"/>
            <el-table-column label="status" width="80">
                <template slot-scope="scope">
                        {{ scope.row.status === 1 ? 'usable' : 'not usable' }}
                </template>
            </el-table-column>
            <el-table-column label="Manipulate" width="280" align='center'>
                <template slot-scope="scope">
                    <el-button type="danger" size="mini" @click="removeDataById(scope.row.id)">delete </el-button>
                    <el-button v-if="scope.row.status==1" type="success" size="mini" @click="lockHospSet(scope.row.id, 0)">lock</el-button>
                    <el-button v-else type="warning" size="mini" @click="lockHospSet(scope.row.id, 1)">unlock </el-button>
                    <router-link :to="'/hospSet/edit/'+scope.row.id">
                        <el-button type="primary" size="mini" icon="el-icon-edit"></el-button>
                    </router-link>
                </template>
            </el-table-column>

        </el-table>
        <!-- 分页 -->
        <el-pagination
            :current-page="page"
            :page-size="limit"
            :total="total"
            style="padding: 30px 0; text-align: center;"
            layout="total, prev, pager, next, jumper"
            @current-change="getList"
        />
    </div>
</template>
<script>

import hospset from '@/api/hospset'

export default {
    data() {
        return {
            current:1,
            limit:3,
            searchObj:{},
            list: [],
            total:0,
            multipleSelection: []
        }

    },
    created() {
        this.getList()
    },
    methods: {
        //get selected hospitals' ids
        handleSelectionChange(selection) {
            this.multipleSelection = selection
        },
        //romove rows
        removeRows() {
            this.$confirm('These deletions cannot be retrieved, continue?', 'Warning', {
                confirmButtonText: 'Confirm',
                cancelButtonText: 'Cancel',
                type: 'warning'
            }).then(() => { 
                var idList = []
                for(var i=0;i<this.multipleSelection.length;i++) {
                    var obj = this.multipleSelection[i]
                    var id = obj.id
                    idList.push(id)
                }
                hospset.batchRemoveHospSet(idList)
                    .then(response => {
                        this.$message({
                        type: 'success',
                        message: 'Successfully deleted!'
                        })
                        this.getList(1)
                    })
            })
        },
        //get hospital set list
        getList(page=1) {
            this.current = page
            hospset.getHospitalSetList(this.current, this.limit, this.searchObj)
                .then(response=> {
                    this.list = response.data.records
                    this.total = response.data.total
                    console.log(response)
                }) 
                .catch(error => {
                    console.log(error)
                }) 
        },
        //delete by id
        removeDataById(id) {
            this.$confirm('Delete hospital set, continue?', 'Warning', {
                confirmButtonText: 'Confirm',
                cancelButtonText: 'Cancel',
                type: 'warning'
                }).then(() => {
                    hospset.deleteHospSet(id)
                        .then(response =>{
                            this.$message({
                                type: 'success',
                                message: 'Successfully deleted!'
                            })
                            this.getList(1)
                        })
                }).catch(() => {
                this.$message({
                    type: 'info',
                    message: 'Delete cancelled!'
                });          
            });
        },
        //lock and unlock hospital
        lockHospSet(id, status) {
            hospset.lockHospSet(id, status)
                .then(response => {
                    this.getList()
                })
        }
    }
}

</script>