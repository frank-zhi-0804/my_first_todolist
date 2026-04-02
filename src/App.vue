<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

// 填入Laf 地址
const BASE_URL = "https://s1w7n86ahv.sealosbja.site"

const str = ref('');
const list = ref([]);

// 页面加载时获取列表
onMounted(() => {
  getList();
});

// 1. 获取任务列表
async function getList() {
  const res = await axios.get(`${BASE_URL}/get_list`);
  // 把数据库数据赋值给 list，页面就渲染了
  list.value = res.data.data;
}

// 2. 添加任务
async function add() {
  if (!str.value.trim()) return;

  // 前端 → 发请求给后端
  await axios.post(`${BASE_URL}/add_todo`, {
    text: str.value
  });

  str.value = '';
  getList(); // 添加后重新拉取数据
}

// 3. 删除任务
async function del(id) {
  await axios.post(`${BASE_URL}/del_todo`, { id });
  getList(); // 删除后重新拉取
}

// 4. 勾选/取消勾选任务
async function update(item) {
  await axios.post(`${BASE_URL}/update_todo`, {
    id: item._id,
    isComplete: item.isComplete
  });
}
</script>

<template>
  <div class="todo-app">
    <div class="title">智的Todo App</div>

    <div class="todo-form">
      <input
        v-model="str"
        type="text"
        class="todo-input"
        placeholder="Add a todo"
        @keyup.enter="add"
      />
      <div @click="add" class="todo-button">Add Todo</div>
    </div>

    <div v-for="item in list" :class="item.isComplete ? 'completed' : 'item'">
      <div>
        <input v-model="item.isComplete" type="checkbox" @change="update(item)" />
        <span class="name">{{ item.text }}</span>
      </div>
      <div @click="del(item._id)" class="del">del</div>
    </div>
  </div>
</template>

<style scoped>
.todo-app {
  box-sizing: border-box;
  margin-top: 40px;
  margin-left: 1%;
  padding-top: 30px;
  width: 98%;
  height: 500px;
  background: #ffffff;
  border-radius: 5px;
}
.title {
  text-align: center;
  font-size: 30px;
  font-weight: 700;
}
.todo-form {
  display: flex;
  margin: 20px 0 30px 20px;
}
.todo-button {
  width: 100px;
  height: 52px;
  border-radius: 0 20px 20px 0;
  text-align: center;
  background: linear-gradient(to right, rgb(113, 65, 168), rgba(44, 114, 251, 1));
  color: #fff;
  line-height: 52px;
  cursor: pointer;
  font-size: 14px;
  user-select: none;
}
.todo-input {
  padding: 0 15px;
  border-radius: 20px 0 0 20px;
  border: 1px solid #dfe1e5;
  outline: none;
  width: 60%;
  height: 50px;
}
.item, .completed {
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 80%;
  height: 50px;
  margin: 8px auto;
  padding: 16px;
  border-radius: 20px;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 20px;
}
.del {
  color: red;
  cursor: pointer;
}
.completed {
  text-decoration: line-through;
  opacity: 0.4;
}
</style>