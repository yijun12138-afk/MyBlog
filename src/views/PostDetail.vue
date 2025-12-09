<template>
  <div class="detail-container">
    <div v-if="loading" class="loading-state">
      <div class="spinner"></div>
      <p>正在加载...</p>
    </div>
    <div v-else-if="post" class="content-wrapper">
      
      <!-- 顶部操作栏 -->
      <div class="action-bar">
        <button class="btn-back" @click="$router.back()">← 返回列表</button>
        <!-- 切换编辑模式按钮 -->
        <button class="btn-edit" @click="toggleEditMode">
          {{ isEditing ? '取消编辑' : '编辑文章' }}
        </button>
      </div>

      <div v-if="!isEditing" class="read-mode">
        <span class="category-tag">个人博客</span>
        <h1 class="title">{{ post.title }}</h1>
        
        <div class="meta">
          <div class="author-info">
            <div class="avatar-circle">{{ post.userId }}</div>
            <span>作者 {{ post.userId }}</span>
          </div>
          <span class="date">Posted on 2025-12-08</span>
        </div>
        <div class="body">
          <p class="lead">{{ post.body }}</p>
        </div>
      </div>
      <div v-else class="edit-mode">
        <h3>修改文章</h3>
        <input v-model="editForm.title" class="edit-input" placeholder="文章标题" />
        <textarea v-model="editForm.body" class="edit-textarea" rows="8" placeholder="正文内容"></textarea>
        <div class="edit-actions">
          <button class="btn-save" @click="savePost">保存修改</button>
        </div>
      </div>

      <hr class="divider" />

      <div class="comments-section">
        <h3 class="comments-header">
          评论 ({{ comments.length }})
        </h3>
        <div class="comment-list">
          <div v-for="comment in comments" :key="comment.id" class="comment-item">
            <div class="comment-avatar">
              {{ comment.email.charAt(0).toUpperCase() }}
            </div>
            <div class="comment-content">
              <div class="comment-meta">
                <span class="comment-email">{{ comment.email }}</span>
                <span class="comment-name">{{ comment.name }}</span>
              </div>
              <p class="comment-body">{{ comment.body }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue'
import { useRoute } from 'vue-router'
import axios from 'axios'

const route = useRoute()
const loading = ref(true)
const post = ref(null)
const comments = ref([]) // 存放评论数据

// 编辑相关状态
const isEditing = ref(false)
const editForm = reactive({ title: '', body: '' })

// 初始化加载
onMounted(async () => {
  const id = route.params.id
  try {
    const [postRes, commentRes] = await Promise.all([
      axios.get(`https://jsonplaceholder.typicode.com/posts/${id}`),
      axios.get(`https://jsonplaceholder.typicode.com/posts/${id}/comments`)
    ])
    post.value = postRes.data
    comments.value = commentRes.data
  } catch (e) {
    alert('加载失败，请检查网络')
  } finally {
    loading.value = false
  }
})

// 切换编辑模式
const toggleEditMode = () => {
  if (!isEditing.value) {
    editForm.title = post.value.title
    editForm.body = post.value.body
  }
  isEditing.value = !isEditing.value
}

// 保存修改 (PUT)
const savePost = async () => {
  try {
    const id = post.value.id
    // 调用 PUT 接口
    const res = await axios.put(`https://jsonplaceholder.typicode.com/posts/${id}`, {
      id: id,
      title: editForm.title,
      body: editForm.body,
      userId: post.value.userId
    })

    // 更新本地视图
    post.value = res.data
    isEditing.value = false
    alert('修改成功！(已模拟发送 PUT 请求)')
  } catch (e) {
    alert('修改失败')
  }
}
</script>

<style scoped>
.detail-container {
  max-width: 800px;
  margin: 0 auto;
  background: #fff;
  min-height: 80vh;
  box-shadow: 0 10px 30px rgba(0,0,0,0.05);
  border-radius: 16px;
  overflow: hidden;
}

.content-wrapper {
  padding: 40px;
}

/* 顶部操作栏 */
.action-bar {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30px;
}

.btn-back {
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  font-weight: 600;
}

.btn-edit {
  background: #e3f2fd;
  color: #1976d2;
  border: none;
  padding: 8px 16px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: 0.3s;
}

.btn-edit:hover {
  background: #bbdefb;
}

/* 阅读模式样式 */
.category-tag {
  color: #3e8e41;
  font-weight: 800;
  text-transform: uppercase;
  font-size: 0.8rem;
  letter-spacing: 1px;
}
.title {
  font-family: 'Lora', serif;
  font-size: 2.2rem;
  margin: 15px 0;
  line-height: 1.3;
  color: #2c3e50;
}
.meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40px;
  padding-bottom: 20px;
  border-bottom: 1px solid #eee;
}
.author-info {
  display: flex;
  align-items: center;
  gap: 10px;
  color: #555;
  font-weight: 600;
}

.avatar-circle {
  width: 32px;
  height: 32px;
  background: #2c3e50;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.9rem;
}

.date {
  color: #999;
  font-size: 0.9rem;
}

.body {
  font-size: 1.1rem;
  line-height: 1.8;
  color: #4a4a4a;
}

.lead {
  font-size: 1.2rem;
  color: #222;
  margin-bottom: 20px;
}

.edit-mode {
  background: #f9f9f9;
  padding: 20px;
  border-radius: 12px;
  border: 1px dashed #ccc;
}

.edit-input, .edit-textarea {
  width: 100%;
  padding: 12px;
  margin-bottom: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-family: inherit;
}

.btn-save {
  background: #3e8e41;
  color: white;
  border: none;
  padding: 10px 25px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
}

/* 评论区样式 */
.divider {
  margin: 60px 0 40px;
  border: none;
  border-top: 1px solid #eee;
}
.comments-header {
  font-family: 'Lora', serif;
  margin-bottom: 30px;
  position: relative;
  display: inline-block;
}
.comments-header::after {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 0;
  width: 40%;
  height: 3px;
  background: #3e8e41;
}
.comment-item {
  display: flex;
  gap: 15px;
  margin-bottom: 25px;
}
.comment-avatar {
  width: 40px;
  height: 40px;
  background: #e0e0e0;
  color: #555;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  flex-shrink: 0;
}
.comment-content {
  background: #f8f9fa;
  padding: 15px;
  border-radius: 0 15px 15px 15px;
  flex: 1;
}
.comment-meta {
  display: flex;
  justify-content: space-between;
  margin-bottom: 8px;
  font-size: 0.85rem;
}
.comment-email {
  font-weight: 700;
  color: #333;
}
.comment-name {
  color: #999;
  font-style: italic;
}
.comment-body {
  font-size: 0.95rem;
  color: #555;
  line-height: 1.5;
  margin: 0;
}

/* 加载动画 */
.loading-state {
  text-align: center;
  padding: 100px 0;
  color: #999;
}
</style>