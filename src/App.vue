<template>
  <div id="app">
    <h1>📋 用户管理 (Vue3 CRUD)</h1>
    
    <!-- ====== 新增/编辑表单 ====== -->
    <div class="form-container">
      <input 
        v-model="formData.name" 
        placeholder="请输入姓名"
        @keyup.enter="saveUser"
      />
      <input 
        v-model="formData.email" 
        placeholder="请输入邮箱"
        @keyup.enter="saveUser"
      />
      <button @click="saveUser" class="btn-primary">
        {{ isEditing ? '✏️ 更新' : '➕ 新增' }}
      </button>
      <button v-if="isEditing" @click="cancelEdit" class="btn-cancel">
        取消
      </button>
    </div>

    <!-- ====== 搜索框 ====== -->
    <div class="search-box">
      <input 
        v-model="searchKeyword" 
        placeholder="🔍 搜索姓名或邮箱..."
        class="search-input"
      />
    </div>

    <!-- ====== 数据表格 ====== -->
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>姓名</th>
          <th>邮箱</th>
          <th>操作</th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="filteredUsers.length === 0">
          <td colspan="4" class="empty-tip">暂无数据，请添加</td>
        </tr>
        <tr v-for="user in filteredUsers" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>
            <button @click="editUser(user)" class="btn-edit">编辑</button>
            <button @click="deleteUser(user.id)" class="btn-delete">删除</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- ====== 统计信息 ====== -->
    <div class="footer">
      <span>总用户数：{{ users.length }}</span>
      <span v-if="searchKeyword">｜ 搜索结果：{{ filteredUsers.length }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      // ---------- 数据 ----------
      users: [
        { id: 1, name: '张三', email: 'zhangsan@example.com' },
        { id: 2, name: '李四', email: 'lisi@example.com' },
        { id: 3, name: '王五', email: 'wangwu@example.com' },
      ],
      
      // ---------- 表单 ----------
      formData: {
        id: null,
        name: '',
        email: ''
      },
      isEditing: false,      // 是否是编辑模式
      nextId: 4,            // 自增ID起始值
      
      // ---------- 搜索 ----------
      searchKeyword: ''
    }
  },
  
  // ---------- 计算属性 ----------
  computed: {
    // 根据关键字过滤用户（不区分大小写）
    filteredUsers() {
      if (!this.searchKeyword.trim()) {
        return this.users
      }
      const keyword = this.searchKeyword.toLowerCase().trim()
      return this.users.filter(user => 
        user.name.toLowerCase().includes(keyword) ||
        user.email.toLowerCase().includes(keyword)
      )
    }
  },
  
  // ---------- 方法 ----------
  methods: {
    // 保存（新增或更新）
    saveUser() {
      // 简单校验
      if (!this.formData.name.trim() || !this.formData.email.trim()) {
        alert('姓名和邮箱不能为空')
        return
      }
      
      if (this.isEditing) {
        // ---- 编辑模式：更新 ----
        const index = this.users.findIndex(u => u.id === this.formData.id)
        if (index !== -1) {
          this.users[index] = { ...this.formData }
        }
        this.cancelEdit()
      } else {
        // ---- 新增模式：添加 ----
        const newUser = {
          id: this.nextId++,
          name: this.formData.name.trim(),
          email: this.formData.email.trim()
        }
        this.users.push(newUser)
        this.resetForm()
      }
    },
    
    // 编辑（回填数据到表单）
    editUser(user) {
      this.formData = { ...user }   // 浅拷贝，避免直接引用
      this.isEditing = true
    },
    
    // 删除（按ID过滤）
    deleteUser(id) {
      if (confirm('确定要删除该用户吗？')) {
        this.users = this.users.filter(user => user.id !== id)
        // 如果删除的是正在编辑的，取消编辑状态
        if (this.isEditing && this.formData.id === id) {
          this.cancelEdit()
        }
      }
    },
    
    // 取消编辑
    cancelEdit() {
      this.resetForm()
      this.isEditing = false
    },
    
    // 重置表单
    resetForm() {
      this.formData = {
        id: null,
        name: '',
        email: ''
      }
    }
  }
}
</script>

<!-- ====== 样式 ====== -->
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #f0f2f5;
  padding: 20px;
}

#app {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

h1 {
  color: #1a1a2e;
  margin-bottom: 24px;
  font-size: 24px;
  border-bottom: 3px solid #4a90d9;
  padding-bottom: 12px;
}

/* ---------- 表单 ---------- */
.form-container {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-bottom: 16px;
}

.form-container input {
  flex: 1;
  min-width: 150px;
  padding: 10px 14px;
  border: 1px solid #d9d9d9;
  border-radius: 6px;
  font-size: 14px;
  transition: border-color 0.2s;
}

.form-container input:focus {
  outline: none;
  border-color: #4a90d9;
  box-shadow: 0 0 0 3px rgba(74, 144, 217, 0.1);
}

/* ---------- 按钮 ---------- */
button {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-primary {
  background: #4a90d9;
  color: white;
}
.btn-primary:hover {
  background: #357abd;
}

.btn-cancel {
  background: #f0f0f0;
  color: #666;
}
.btn-cancel:hover {
  background: #e0e0e0;
}

.btn-edit {
  background: #52c41a;
  color: white;
  margin-right: 6px;
  padding: 6px 14px;
}
.btn-edit:hover {
  background: #45a818;
}

.btn-delete {
  background: #ff4d4f;
  color: white;
  padding: 6px 14px;
}
.btn-delete:hover {
  background: #e04345;
}

/* ---------- 搜索 ---------- */
.search-box {
  margin-bottom: 16px;
}

.search-input {
  width: 100%;
  padding: 10px 14px;
  border: 1px solid #d9d9d9;
  border-radius: 6px;
  font-size: 14px;
  background: #fafafa;
}

.search-input:focus {
  outline: none;
  border-color: #4a90d9;
  background: white;
}

/* ---------- 表格 ---------- */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 8px;
}

thead {
  background: #fafafa;
}

th {
  text-align: left;
  padding: 12px 14px;
  font-weight: 600;
  color: #1a1a2e;
  border-bottom: 2px solid #e8e8e8;
}

td {
  padding: 12px 14px;
  border-bottom: 1px solid #f0f0f0;
  color: #333;
}

tr:hover td {
  background: #fafafa;
}

.empty-tip {
  text-align: center;
  color: #999;
  padding: 30px 0;
}

/* ---------- 底部统计 ---------- */
.footer {
  margin-top: 16px;
  padding-top: 16px;
  border-top: 1px solid #f0f0f0;
  color: #888;
  font-size: 14px;
  display: flex;
  gap: 20px;
}
</style>