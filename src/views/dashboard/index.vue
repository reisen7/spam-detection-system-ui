<template>
  <div class="dashboard-container">
    <el-row :gutter="20">
      <el-col :span="8">
        <el-card shadow="hover" class="user-card mgb20">
          <div class="user-info">
            <img src="https://www.dmoe.cc/random.php" class="user-avator" alt />
            <div class="user-info-cont">
              <div class="user-info-name">admin</div>
              <div class="user-info-job">管理员</div>
            </div>
          </div>
          <div class="user-info-list">
            上次登录时间：
            <span>2024-01-08</span>
          </div>
          <div class="user-info-list">
            上次登录地点：
            <span>太原</span>
          </div>
        </el-card>
        <el-card shadow="hover" class="todo-card">
          <div slot="header" class="clearfix">
            <span>待办事项</span>
            <el-button style="float: right; padding: 3px 0" type="text" @click="addTodo">添加</el-button>
          </div>
          <el-table :show-header="false" :data="todoList" style="width:100%;">
            <el-table-column width="40">
              <template slot-scope="scope">
                <el-checkbox v-model="scope.row.status"></el-checkbox>
              </template>
            </el-table-column>
            <el-table-column>
              <template slot-scope="scope">
                <div class="todo-item" :class="{ 'todo-item-del': scope.row.status }">{{ scope.row.title }}</div>
              </template>
            </el-table-column>
            <el-table-column width="60">
              <template slot-scope="scope">
                <i class="el-icon-edit" @click="editTodo(scope.$index)"></i>
                <i class="el-icon-delete" @click="deleteTodo(scope.$index)"></i>
              </template>
            </el-table-column>
          </el-table>
          <el-dialog title="添加待办事项" :visible.sync="dialogVisible" width="30%">
            <el-input v-model="newTodoTitle" placeholder="请输入待办事项"></el-input>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="confirmAddTodo">确 定</el-button>
            </span>
          </el-dialog>
        </el-card>
      </el-col>
      <el-col :span="16">
        <el-row :gutter="20" class="mgb20">
          <el-col :span="8">
            <el-card shadow="hover" :body-style="{ padding: '0px' }" class="grid-card">
              <div class="grid-content grid-con-1">
                <i class="el-icon-lx-people grid-con-icon"></i>
                <div class="grid-cont-right">
                  <div class="grid-num">1</div>
                  <div>用户访问量</div>
                </div>
              </div>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="hover" :body-style="{ padding: '0px' }" class="grid-card">
              <div class="grid-content grid-con-2">
                <i class="el-icon-lx-notice grid-con-icon"></i>
                <div class="grid-cont-right">
                  <div class="grid-num">1</div>
                  <div>系统消息</div>
                </div>
              </div>
            </el-card>
          </el-col>
          <el-col :span="8">
            <el-card shadow="hover" :body-style="{ padding: '0px' }" class="grid-card">
              <div class="grid-content grid-con-3">
                <i class="el-icon-lx-goods grid-con-icon"></i>
                <div class="grid-cont-right">
                  <div class="grid-num">4</div>
                  <div>待办事项数量</div>
                </div>
              </div>
            </el-card>
          </el-col>
        </el-row>
        <!-- <el-row :gutter="20">
          <el-col :span="12">
            <el-card shadow="hover">
              <schart ref="bar" class="schart" canvasId="bar" :options="options"></schart>
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-card shadow="hover">
              <schart ref="line" class="schart" canvasId="line" :options="options2"></schart>
            </el-card>
          </el-col>
        </el-row> -->
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'Dashboard',
  data () {
    return {
      todoList: [
        {
          title: '已经查看',
          status: false
        },
        {
          title: '成功',
          status: false
        }
      ],
      dialogVisible: false,
      newTodoTitle: ''
    };
  },
  methods: {
    addTodo () {
      this.dialogVisible = true;
      this.newTodoTitle = '';
    },
    confirmAddTodo () {
      if (this.newTodoTitle.trim()) {
        this.todoList.push({ title: this.newTodoTitle, status: false });
        this.dialogVisible = false;
      } else {
        this.$message.error('请填写待办事项');
      }
    },
    deleteTodo (index) {
      this.todoList.splice(index, 1);
    },
    editTodo (index) {
      this.$prompt('请输入新的待办事项', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        inputValue: this.todoList[index].title
      }).then(({ value }) => {
        if (value.trim()) {
          this.todoList[index].title = value;
        } else {
          this.$message.error('请填写待办事项');
        }
      }).catch(() => { });
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard-container {
  margin: 30px;
}

.el-row {
  margin-bottom: 20px;
}

.user-card {
  background-color: #f9fafc;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

  .user-info {
    display: flex;
    align-items: center;
    padding-bottom: 20px;
    border-bottom: 1px solid #ebeef5;
    margin-bottom: 20px;

    .user-avator {
      width: 100px;
      height: 100px;
      border-radius: 50%;
    }

    .user-info-cont {
      padding-left: 20px;
      flex: 1;
      font-size: 14px;
      color: #606266;

      .user-info-name {
        font-size: 24px;
        color: #303133;
        margin-bottom: 5px;
      }

      .user-info-job {
        font-size: 16px;
        color: #909399;
      }
    }
  }

  .user-info-list {
    font-size: 14px;
    color: #909399;
    line-height: 25px;

    span {
      margin-left: 10px;
      color: #303133;
    }
  }
}

.todo-card {
  background-color: #f9fafc;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

  .todo-item {
    font-size: 14px;
    color: #303133;

    &.todo-item-del {
      text-decoration: line-through;
      color: #909399;
    }
  }
}

.grid-card {
  background-color: #f9fafc;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

  .grid-content {
    display: flex;
    align-items: center;
    height: 100px;

    .grid-con-icon {
      font-size: 50px;
      width: 100px;
      height: 100px;
      text-align: center;
      line-height: 100px;
      color: #fff;
    }

    .grid-cont-right {
      flex: 1;
      text-align: center;
      font-size: 14px;
      color: #909399;

      .grid-num {
        font-size: 30px;
        font-weight: bold;
        color: #303133;
      }
    }
  }

  .grid-con-1 .grid-con-icon {
    background: #409EFF;
  }

  .grid-con-2 .grid-con-icon {
    background: #67C23A;
  }

  .grid-con-3 .grid-con-icon {
    background: #F56C6C;
  }
}
</style>
