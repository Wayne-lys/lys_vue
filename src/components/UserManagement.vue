<template>
    <div>
      <el-input v-model="searchKeyword" placeholder="请输入关键词" style="width: 200px; margin-bottom: 20px;" />
      <el-button type="primary" @click="searchUser">搜索</el-button>
      <el-button type="success" @click="addUser">新增用户</el-button>
  
      <el-table :data="filteredUsers" style="width: 100%">
        <el-table-column prop="name" label="姓名" />
        <el-table-column prop="gender" label="性别" />
        <el-table-column prop="age" label="年龄" />
        <el-table-column prop="phone" label="电话" />
        <el-table-column label="操作">
          <template #default="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
  
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[5, 10, 20]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="filteredUsers.length"
      />
  
      <el-dialog :title="dialogTitle" v-model="dialogVisible">
        <el-form :model="form">
          <el-form-item label="姓名">
            <el-input v-model="form.name" />
          </el-form-item>
          <el-form-item label="性别">
            <el-input v-model="form.gender" />
          </el-form-item>
          <el-form-item label="年龄">
            <el-input v-model="form.age" />
          </el-form-item>
          <el-form-item label="电话">
            <el-input v-model="form.phone" />
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取消</el-button>
            <el-button type="primary" @click="saveUser">确定</el-button>
          </span>
        </template>
      </el-dialog>
    </div>
  </template>
  
  <script>
  import { ref, computed, reactive } from 'vue';
  
  export default {
    setup() {
      const users = ref([
        { name: '张三', gender: '男', age: 25, phone: '12345678901' },
        { name: '李四', gender: '女', age: 30, phone: '12345678902' },
        { name: '王五', gender: '男', age: 28, phone: '12345678903' },
      ]);
      const searchKeyword = ref('');
      const currentPage = ref(1);
      const pageSize = ref(5);
      const dialogVisible = ref(false);
      const dialogTitle = ref('新增用户');
      const form = reactive({
        name: '',
        gender: '',
        age: '',
        phone: '',
      });
      const editIndex = ref(-1);
  
      const filteredUsers = computed(() => {
        return users.value.filter(user => {
          return Object.values(user).some(value => value.toString().includes(searchKeyword.value));
        });
      });
  
      const searchUser = () => {
        currentPage.value = 1;
      };
  
      const addUser = () => {
        dialogTitle.value = '新增用户';
        dialogVisible.value = true;
        form.name = '';
        form.gender = '';
        form.age = '';
        form.phone = '';
        editIndex.value = -1;
      };
  
      const handleEdit = (index, row) => {
        dialogTitle.value = '编辑用户';
        dialogVisible.value = true;
        form.name = row.name;
        form.gender = row.gender;
        form.age = row.age;
        form.phone = row.phone;
        editIndex.value = index;
      };
  
      const handleDelete = (index, row) => {
        users.value.splice(index, 1);
      };
  
      const saveUser = () => {
        if (editIndex.value === -1) {
          users.value.push({ ...form });
        } else {
          users.value.splice(editIndex.value, 1, { ...form });
        }
        dialogVisible.value = false;
      };
  
      const handleSizeChange = (val) => {
        pageSize.value = val;
        currentPage.value = 1;
      };
  
      const handleCurrentChange = (val) => {
        currentPage.value = val;
      };
  
      return {
        users,
        searchKeyword,
        currentPage,
        pageSize,
        dialogVisible,
        dialogTitle,
        form,
        filteredUsers,
        searchUser,
        addUser,
        handleEdit,
        handleDelete,
        saveUser,
        handleSizeChange,
        handleCurrentChange,
      };
    },
  };
  </script>
  
  <style scoped>
  .el-pagination {
    margin-top: 20px;
  }
  </style>