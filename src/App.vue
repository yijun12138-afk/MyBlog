<template>
  <div class="blog-container">
    <!-- 顶部导航 -->
    <header class="header">
      <div class="brand">MyBlog.</div>
      <nav class="nav-links">
        <router-link to="/">封面</router-link>
        <router-link to="/blog">文章</router-link>
        <router-link to="/about">关于</router-link>
      </nav>
    </header>

    <!-- 路由视图 (带有淡入淡出效果) -->
    <main class="main-content">
      <router-view v-slot="{ Component }">
        <transition name="fade" mode="out-in">
          <component :is="Component" />
        </transition>
      </router-view>
    </main>

    <!-- 页脚 -->
    <footer class="footer">
      <p>&copy; 2025 Written with ❤️ by Me.</p>
    </footer>
  </div>
</template>

<style>
/* 引入谷歌字体 */
@import url('https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,600;1,400&family=Nunito:wght@300;400;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  /* 背景色：温暖的米白色 */
  background-color: #f9f7f2; 
  color: #2c3e50;
  font-family: 'Nunito', sans-serif; /* 正文用无衬线 */
  line-height: 1.6;
}

.blog-container {
  max-width: 900px;
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  padding: 0 20px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 40px 0;
  border-bottom: 1px solid rgba(0,0,0,0.05);
}

.brand {
  font-size: 1.8rem;
  font-weight: 600;
  letter-spacing: -1px;
}

.nav-links a {
  text-decoration: none;
  color: #666;
  margin-left: 25px;
  font-size: 0.95rem;
  transition: color 0.3s;
  position: relative;
}

.nav-links a:hover,
.nav-links a.router-link-active {
  color: #2c3e50;
  font-weight: 700;
}

.nav-links a.router-link-active::after {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 50%;
  transform: translateX(-50%);
  width: 4px;
  height: 4px;
  background: #3e8e41; 
  border-radius: 50%;
}

.main-content {
  flex: 1; /* 让内容区撑开高度 */
  padding: 40px 0;
}

.footer {
  text-align: center;
  padding: 40px 0;
  font-size: 0.8rem;
  color: #999;
  border-top: 1px solid rgba(0,0,0,0.05);
}

/* 页面切换动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease, transform 0.4s ease;
}
.fade-enter-from { opacity: 0; transform: translateY(10px); }
.fade-leave-to { opacity: 0; transform: translateY(-10px); }
</style>