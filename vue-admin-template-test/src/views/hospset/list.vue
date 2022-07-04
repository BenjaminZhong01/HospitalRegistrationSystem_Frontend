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

        <el-table
            :data="list" stripe style="width: 100%">

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
            total:0
        }

    },
    created() {
        this.getList()
    },
    methods: {
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
        }
    }
}

</script>