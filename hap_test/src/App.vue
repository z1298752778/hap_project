<template>
  <div class="container">
    <!-- 主表 -->
    <div class="master-table">
      <h2>部门列表</h2>
      <table>
        <thead>
          <tr>
            <th>部门ID</th>
            <th>部门名称</th>
            <th>所在地</th>
          </tr>
        </thead>
        <tbody>
          <tr 
            v-for="dept in departments" 
            :key="dept.id"
            @click="selectDepartment(dept)"
            :class="{ 'selected': selectedDepartment?.id === dept.id }"
          >
            <td>{{ dept.id }}</td>
            <td>{{ dept.name }}</td>
            <td>{{ dept.location }}</td>
          </tr>
        </tbody>
      </table>
    </div> 
    <br/>
    <!-- 从表 -->
    <div class="detail-table">
      <h2>员工列表</h2>
      <table>
        <thead>
          <tr>
            <th>员工ID</th>
            <th>姓名</th>
            <th>职位</th>
            <th>薪资</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="emp in filteredEmployees" :key="emp.id">
            <td>{{ emp.id }}</td>
            <td>{{ emp.name }}</td>
            <td>{{ emp.position }}</td>
            <td>{{ emp.salary }}</td>
          </tr>
        </tbody>
      </table>
      <div v-if="!filteredEmployees.length" class="no-data">
        请先选择一个部门查看员工
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 模拟主表数据
const departments = ref([
  { id: 1, name: '技术部', location: '3楼' },
  { id: 2, name: '市场部', location: '5楼' },
  { id: 3, name: '财务部', location: '8楼' }
])

// 模拟从表数据
const employees = ref([
  { id: 1, deptId: 1, name: '张三', position: '前端工程师', salary: 15000 },
  { id: 2, deptId: 1, name: '李四', position: '后端工程师', salary: 18000 },
  { id: 3, deptId: 2, name: '王五', position: '市场经理', salary: 20000 },
  { id: 4, deptId: 2, name: '赵六', position: '推广专员', salary: 8000 },
  { id: 5, deptId: 3, name: '陈七', position: '会计', salary: 12000 }
])

// 选中的部门
const selectedDepartment = ref(null)

// 选择部门方法
const selectDepartment = (dept) => {
  selectedDepartment.value = dept
}

// 计算属性：过滤出属于选中部门的员工
const filteredEmployees = computed(() => {
  if (!selectedDepartment.value) return []
  return employees.value.filter(emp => emp.deptId === selectedDepartment.value.id)
})
</script>

<style scoped>
.container {
  display: flex;
  gap: 20px;
  padding: 20px;
}

.master-table, .detail-table {
  flex: 1;
  border: 1px solid #eee;
  padding: 15px;
  border-radius: 8px;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 10px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f5f5f5;
}

tr:hover {
  background-color: #f9f9f9;
  cursor: pointer;
}

.selected {
  background-color: #e3f2fd;
}

.no-data {
  padding: 20px;
  text-align: center;
  color: #999;
}
</style>