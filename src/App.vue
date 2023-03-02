<script setup>
import axios from 'axios'
import {reactive, ref, onMounted} from "vue";
import {ElMessageBox} from 'element-plus'

  const books = reactive([])
  const getStudents = () => {
    axios.get("http://127.0.0.1:5000/books/",).then(res => {
      books.splice(0, books.length)
      books.push(...res.data.results)
      console.log('更新数据')
    })
  }

  onMounted(() => {
    getStudents()
  })
  const handleDelete = (index, scope) => {
    console.log(index, scope)
    axios.delete(`http://localhost:5000/books/${scope.id}`).then(() => {
      getStudents()
    })
  }

  const add_dialog_visible = ref(false)
  const ruleFormRef = ref()
  const book_form = reactive({
    book_number: "",
    book_name: "",
    book_type: "",
    book_prize: "",
    author: "",
    book_publisher: "",
    id: "",
  })
  const submitForm = (formEl) => {
    axios.post('http://localhost:5000/books', book_form).then(() => {
      add_dialog_visible.value = false
      formEl.resetFields()
      getStudents()
    })
  }

  const resetForm = (formEl) => {
    formEl.resetFields()
  }
  const handleClose = (done) => {
  ElMessageBox.confirm('确认关闭? ')
      .then(() =>
      {
        done();
      })
        .catch(() => {});
  }
  const editFormRef = ref()
  const edit_dialog_visible = ref(false)
  const handleEdit = (index,scope) => {
  for (let key in scope) {
    book_form[key] = scope[key]
  }
  edit_dialog_visible.value = true
  }
  // 编辑提交按钮
  const submitEditForm = (formEl) => {
    axios.put(`http://localhost:5000/books/${book_form.id}`, book_form).then((res) => {
      formEl.resetFields()
      edit_dialog_visible.value = false
      getStudents()
    })
  }


</script>

<template>
  <div style="margin: 0 auto;width: 50%;">
    <h1 style="text-align: center">图书管理系统</h1>
    <el-button type="primary" @click="add_dialog_visible=true" size="small">添加图书</el-button>

    <el-table :data="books" style="margin: 20px auto;">
      <el-table-column label="編号" prop="book_number"/>
      <el-table-column label="书名" prop="book_name"/>
      <el-table-column label="类型" prop="book_type"/>
      <el-table-column label="价格" prop="book_prize"/>
      <el-table-column label="作者" prop="author"/>
      <el-table-column label="出版社" prop="book_publisher"/>
      <el-table-column align="right" label="操作" width="200px">
        <template #default="scope">
          <el-button size="small" @click="handleEdit(scope.$index, scope.row)">
            编辑
          </el-button>
          <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>

  <!-- 添加图书页面 -->
  <el-dialog
      title="添加图书"
      v-model="add_dialog_visible"
      width="30%"
      :before-close="handleClose">
    <el-form
        ref="ruleFormRef"
        :model="book_form"
        status-icon
        label-width="120px"
        class="demo-ruleForm">
      <el-form-item label="编号" prop="book_number">
        <el-input v-model="book_form.book_number" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="书名" prop="book_name">
        <el-input v-model="book_form.book_name" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="类型" prop="book_type">
        <el-input v-model="book_form.book_type" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="价格" prop="book_prize">
        <el-input v-model.number="book_form.book_prize" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="作者" prop="author">
        <el-input v-model="book_form.author" autocomplete="off"/>
      </el-form-item>
      <el-form-item label="出 版社" prop="book_publisher">
        <el-input v-model="book_form.book_publisher" autocomplete="off"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm(ruleFormRef)">提交</el-button>
        <el-button @click="resetForm(ruleFormRef)">重置</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>

  <!--编辑图书页面-->
  <el-dialog title="编辑图书" v-model="edit_dialog_visible" width="30%" :before-close="handleClose">
    <el-form ref="editFormRef" :model="book_form" status-icon label-width="l20px"
            class="demo-ruleForm" >
    <el-form-item label="编号" prop="book_number">
  <el-input v-model="book_form.book_number" autocomplete="off"/>
  </el-form-item>
  <el-form-item label="书名" prop="book_name'">
  <el-input v-model="book_form.book_name" autocomplete="off"/>
  </el-form-item>
  <el-form-item label="类型" prop="book_type">
  <el-input v-model="book_form.book_type" autocomplete="off"/>
  </el-form-item>
  <el-form-item label="价格" prop="book_prize">
  <el-input v-model.number= "book_form.book_prize" autocomplete= "off"/>
  </el-form-item>
  <el-form-item label="作者" prop="author">
  <el-input v-model="book_form.author" autocomplete="off"/>
  </el-form-item>
  <el-form-item label="出版社" prop="book_publisher">
  <el-input v-model= "book_form.book_publisher" autocomplete= "off"/>
  </el-form-item>
  <el-form-item>
  <el-button type="primary" @click="submitEditForm(editFormRef)">提交</el-button>
  <el-button @click="resetForm(editFormRef)">重置</el-button>
  </el-form-item>
  </el-form>
  </el-dialog>



</template>

<style scoped>
</style>
