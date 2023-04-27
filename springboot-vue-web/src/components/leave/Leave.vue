<template>

  <div>


  <el-steps :active="active" finish-status="success">
    <el-step title="请假"></el-step>
    <el-step title="请假中"></el-step>
    <el-step title="允许请假"></el-step>
  </el-steps>

  <el-button style="margin-top: 12px;" @click="next"  v-if="user.roleId===2">请假</el-button>
  <el-button style="margin-top: 12px;" @click="next"   v-if="user.roleId===0">允许</el-button>
  </div>

</template>

<script>
export default {
  name: "Leave",
  data() {
    return {
      user: JSON.parse(sessionStorage.getItem('CurUser')),
      active: 0
    };
  },

  methods: {
    next() {
      if (this.active++ > 2) this.active = 0;
    }
  }
}
</script>

<style scoped>

</style>