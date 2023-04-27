<template>
  <!-- 表头设置和表的边框-->
  <div>
    <div style="margin-left: 5px;margin-bottom: 20px">
      <el-button type="primary " @click="add" v-if="user.roleId==0">新增公告</el-button>

      <div class="scroll-container" style="margin-left: 300px;width: 400px">
        <div class="scroll-content" :style="{ animationDuration: duration }">
          <div v-model="list" class="scroll-item" style="color: crimson;font-size: large">
            {{ list }}
          </div>
        </div>
      </div>

    </div>
    <el-table :data="tableData"
              :header-cell-style="{textAlign: 'center',background: '#f2f5fc',color:'#555555'}"
    >
      <el-table-column prop="notice" label="公告" height="200" width="800" style="color: crimson" >
      </el-table-column>

      <el-table-column prop="operate" label="操作" width="400"  v-if="user.roleId==0" >
        <template slot-scope="scope">
          <el-button type="success" @click="mod(scope.row)" style="margin-right: 20px">修改公告</el-button>
          <el-popconfirm
              confirm-button-text='好的'
              cancel-button-text='不用了'
              icon="el-icon-info"
              icon-color="red"
              title="确定删除吗？"
              @confirm="del(scope.row.id)"
              style="margin-left: 15px"
          >
            <el-button slot="reference" type="danger" >删除公告</el-button>




          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>


    <el-dialog
        title="提示"
        :visible.sync="centerDialogVisible"
        width="60%"

        center>
      <el-form ref="form" :model="form" :rules="rules" label-width="200px"  label-height="100px">
        <el-form-item label="公告" prop="notice">
          <el-input type="textarea" v-model="form.notice"></el-input>
        </el-form-item>


      </el-form>
      <span slot="footer" class="dialog-footer">
    <el-button @click="centerDialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="save">确 定</el-button>
  </span>
    </el-dialog>

    <div class="block" style="margin-top: 1px;text-align: center">
      <el-carousel height="500px">

        <el-carousel-item>
          <img src="../assets/gz2.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/gz3.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>

        <el-carousel-item>
          <img src="../assets/gz4.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/gz5.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/gz6.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>
        <el-carousel-item>
          <img src="../assets/www.jpg" alt="" height="100%" width="90%">
        </el-carousel-item>

      </el-carousel>
    </div>
  </div>








</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "HPage",
  data() {
    return {
      notice:'',
      list: [], // 文字数据
      duration: "" ,// 动画时间
      registerShow:false,
      user: JSON.parse(sessionStorage.getItem('CurUser')),
      tableData: [],
      centerDialogVisible: false,
      form: {
        id:'',
        notice:''

      },
      rules:{
        notice: [
          { required: true, message: '请填写活公告', trigger: 'blur' }
        ]
      },
    }
  },
  methods: {
    calculateDuration(item) {
      const length = item.length;
      const seconds = length/2; // 每秒10个字的速度
      return `${seconds}s`;
    },
    // 更新文字数据
    updateList() {
      // 模拟异步获取数据
      setTimeout(() => {
          const newData = this.tableData[0].notice;
          this.list = newData;
          console.log(this.list)
          // 根据新数据计算动画时间
          this.duration = this.calculateDuration(newData);
          // 递归调用更新文字数据
          this.updateList();
      }, 500);
    },

    async loadGet() {
      await this.$axios.get(this.$httpUrl + '/notice/list').then(res => res.data).then(res => {
        this.tableData=res.data
      })
    },
    del(id){
      this.$axios.get(this.$httpUrl + '/notice/del?id='+id,
      ).then(res => res.data).then(res => {
        if (res.code === 200) {
          this.$message({
                message: '操作成功',
                type: 'success'
              }
          )
          this.loadGet()
        } else {
          this.$message.error('操作失败');
        }
      })

    },
    mod(row){
      this.form.id=row.id
      this.form.notice=row.notice
      this.centerDialogVisible = true
    },
    save() {
      if (this.form.id){
        this.$axios.post(this.$httpUrl + '/notice/update', this.form
        ).then(res => res.data).then(res => {
          if (this.form.id) {
            this.$message({
                  message: '修改成功',
                  type: 'success'
                }
            )
            this.centerDialogVisible = false
            this.loadGet()
          } else {
            this.$message.error('修改失败');

          }
        })
      }
      else {
        this.$axios.post(this.$httpUrl + '/notice/save', this.form
        ).then(res => res.data).then(res => {

          if (res.code == 200) {
            this.$message({
                  message: '操作成功',
                  type: 'success'
                }
            )
            this.centerDialogVisible = false
            this.loadGet()
          } else {
            this.$message.error('操作失败');

          }
        })
      }

    },


    resetForm() {
      this.form.notice=''

    },
    add() {
      this.centerDialogVisible = true
      this.$nextTick(() => {
        this.resetForm();
      })
    }
  },
  mounted() {
    this.updateList();
    this.loadGet();
  },
  beforeMount() {
    this.loadGet();


  }
}

</script>

<style  scoped>
.scroll-container {
  width: 100%;
  height: 40px;
  overflow: hidden;
  white-space: nowrap;
}

.scroll-content {
  display: inline-block;
  animation-name: scroll;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
  animation-direction: reverse;
}

.scroll-item {
  display: inline-block;
  margin-left: 300px;
}

@keyframes scroll {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-100%);
  }
}




</style>