<template>
  <div class="app-container">
      Add hospital
      <el-form label-width="120px">
         <el-form-item label="Hospital Name">
            <el-input v-model="hospitalSet.hosname"/>
         </el-form-item>
         <el-form-item label="Hospital Index">
            <el-input v-model="hospitalSet.hoscode"/>
         </el-form-item>
         <el-form-item label="API Basic Path">
            <el-input v-model="hospitalSet.apiUrl"/>
         </el-form-item>
         <el-form-item label="Contact Name">
            <el-input v-model="hospitalSet.contactsName"/>
         </el-form-item>
         <el-form-item label="Contact Phone">
            <el-input v-model="hospitalSet.contactsPhone"/>
         </el-form-item>
         <el-form-item>
            <el-button type="primary" @click="saveOrUpdate">Save</el-button>
         </el-form-item>
      </el-form>
   </div>
</template>
<script>
import hospset from '@/api/hospset'

export default {
    data() {
        return {
            hospitalSet :{} 
        } 
    },
    created() {
        if(this.$route.params && this.$route.params.id) {
            const id = this.$route.params.id
            this.getHospSet(id)
        } else {
            this.hospitalSet = {}
        }
    },
    methods: {
        getHospSet(id) {
            hospset.getHospSet(id)
                .then(response => {
                    this.hospitalSet = response.data
                })
        },
        save() {
            hospset.saveHospSet(this.hospitalSet)
                .then(response =>{
                    this.$message({
                        type: 'success',
                        message: 'Successfully saved!'
                    })
                    this.$router.push({path:'/hospSet/list'})
                })
        },
        update() {
            hospset.updateHospSet(this.hospitalSet)
                .then(response => {
                    this.$message({
                        type: 'success',
                        message: 'Successfully edited!'
                    })
                    this.$router.push({path:'/hospSet/list'})
                })
        },
        saveOrUpdate() {
            if(!this.hospitalSet.id) {
                this.save();
            } else {
                this.update()
            }
        }
    }

}
</script>
