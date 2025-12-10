<template>
  <div class="about-container">
    <div class="card about-card">
      <div class="profile-section">
        <img 
          src="https://github.com/yijun12138-afk.png" 
          alt="Avatar" 
          class="avatar"
        />
        <h2 class="name">陈奕君</h2>
        <p class="role">24软件工程C班</p>
      </div>
      <div class="bio-section">
        <p>
          👋 你好！这是一个基于 <strong>Vue 3</strong> 构建的个人博客系统。
        </p>
        <p>
          MyBlog 是一个基于 Vue 3 生态构建的单页应用（SPA）。
          项目采用了现代化的 Composition API 风格开发，旨在提供流畅的用户体验。
          用户可以在网站中浏览文章列表、通过关键词实时搜索文章、查看文章详情及评论。
          同时，项目实现了模拟的后台管理功能，支持文章发布、单条删除、批量删除以及文章内容的编辑修改。
          前端通过 Axios 与远程 API 进行交互，实现了完整的数据流转。
        </p>
      </div>
      <div class="social-links">
        <a 
          href="https://github.com/yijun12138-afk" 
          target="_blank" 
          class="link-item github"
        >
          <span>GitHub</span>
        </a>
        <div class="link-item email" @click="copyEmail">
          <span>{{ email }}</span>
          <transition name="fade">
            <span v-if="showCopied" class="copy-tooltip">已复制!</span>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const email = '1711561713@qq.com'
const showCopied = ref(false)
const copyEmail = async () => {
  try {
    await navigator.clipboard.writeText(email)
    showCopied.value = true
    setTimeout(() => {
      showCopied.value = false
    }, 2000)
  } catch (err) {
    alert('复制失败，请手动复制')
  }
}
</script>

<style scoped>
.about-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh; 
  padding: 20px;
}
.about-card {
  background: white;
  width: 100%;
  max-width: 500px;
  padding: 40px;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  text-align: center;
  transition: transform 0.3s;
}
.about-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.1);
}
.avatar {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  border: 4px solid #f0f2f5;
  margin-bottom: 20px;
  object-fit: cover;
}
.name {
  font-family: 'Lora', serif;
  font-size: 2rem;
  color: #2c3e50;
  margin-bottom: 5px;
}
.role {
  color: #3e8e41; 
  font-weight: 600;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-bottom: 30px;
}
.bio-section {
  color: #666;
  line-height: 1.8;
  margin-bottom: 40px;
  text-align: left;
  background: #f9f9f9;
  padding: 20px;
  border-radius: 10px;
}
.bio-section strong {
  color: #2c3e50;
}
.social-links {
  display: flex;
  justify-content: center;
  gap: 20px;
}
.link-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 25px;
  border-radius: 50px;
  text-decoration: none;
  font-weight: bold;
  transition: all 0.3s;
  cursor: pointer;
}
.github {
  background-color: #24292e;
  color: white;
}
.github:hover {
  background-color: #000;
  transform: scale(1.05);
}
.email {
  background-color: #f1f8e9;
  color: #3e8e41;
}
.email:hover {
  background-color: #dcedc8;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s, transform 0.3s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: translate(-50%, 10px);
}
</style>