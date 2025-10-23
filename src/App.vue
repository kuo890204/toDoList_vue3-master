// Todo 輸入框、刪除
// ref()、reactive()
/**
 * npm init vue@latest
 * 
 * npm install bootstrap  
 * import "bootstrap/dist/css/bootstrap.min.css"; // 樣式框架安裝
 * 
 * npm install --save-dev json-server@latest
 * "mock-api": "json-server --watch db.json --port 3001" //npx 本地虛擬json資料檔
 * npm run mock-api
 * 
 */
<script setup>
  import { onMounted, ref } from "vue";
  import axios from "axios";

  const tab = ref("firstPage");

  const toDoList = ref([]);       // 存資料
  const newToDo = ref('')           // 新增任務輸入框
  const apiUrl = 'http://localhost:3001/toDoList'; // JSON Server 位置
  
// 🔹 讀取資料（GET）
  async function loadData() {
    try {
      const res = await axios.get(apiUrl)
      toDoList.value = res.data
    } catch (err) {
      console.error('讀取失敗:', err)
    }
  }

// 🔹 新增任務（POST）
  async function addTask() {
    if (!newToDo.value.trim()) return
    try {
      const res = await axios.post(apiUrl, {
        name: newToDo.value,
        content: '尚未填寫內容',
        isChecked: false
      })
      toDoList.value.push(res.data)
      newToDo.value = ''
    } catch (err) {
      console.error('新增失敗:', err)
    }
  }

// 🔹 切換勾選狀態（PATCH）
  async function toggleCheck(item) {
    try {
      item.isChecked = !item.isChecked
      await axios.patch(`${apiUrl}/${item.id}`, { isChecked: item.isChecked })
    } catch (err) {
      console.error('更新失敗:', err)
    }
  }

// 🔹 刪除項目（DELETE）
  async function deleteTask(id) {
    try {
      await axios.delete(`${apiUrl}/${id}`)
      toDoList.value = toDoList.value.filter(t => t.id !== id)
    } catch (err) {
      console.error('刪除失敗:', err)
    }
  }
  
  onMounted(() => {
    loadData();
  })
  
</script>

<template>
  <div class="container mt-4">
    <h1 class="text-center mb-4">資料總覽</h1>

    <!-- 頁籤切換 -->
    <ul class="nav nav-tabs mb-3 justify-content-center">
      <li class="nav-item">
        <button class="nav-link" :class="{ active: tab === 'firstPage' }" @click="tab = 'firstPage'">
          直式表格
        </button>
      </li>
      <li class="nav-item">
        <button class="nav-link" :class="{ active: tab === 'secondPage' }" @click="tab = 'secondPage'">
          橫式表格
        </button>
      </li>
    </ul>

    <!-- 第一頁：直式表格 -->
    <div v-show="tab === 'firstPage'" class="card p-3 shadow-sm">
      <table class="table table-bordered text-center align-middle">
        <thead class="table-primary">
          <tr>
            <th>項目ID</th>
            <th>項目名稱</th>
            <th>項目內容</th>
            <th>完成與否</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in toDoList" :key="item.id" :class="item.isChecked ? 'table-success' : 'table-danger'">
            <td>{{ item.id }}</td>
            <td>{{ item.name }}</td>
            <td>{{ item.content }}</td>
            <td>
              <input type="checkbox" @click="toggleCheck(item)" v-model="item.isChecked"></input>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- 第二頁：橫式表格 -->
    <div v-show="tab === 'secondPage'" class="card p-3 shadow-sm">
      <table class="table table-bordered">
        <thead class="table-primary">
          <tr>
            <th class=" text-center">標題</th>
            <th class=" text-center">內容</th>
          </tr>
        </thead>
        <tbody>
          <tr><th class="table-light w-25 text-center">姓名</th><td>王小明</td></tr>
          <tr><th class="table-light text-center">年齡</th><td>28</td></tr>
          <tr><th class="table-light text-center">職業</th><td>工程師</td></tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped></style>
