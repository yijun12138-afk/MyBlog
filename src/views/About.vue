<template>
  <div class="about-page">
    <div class="profile-img">
      <span>Me</span>
    </div>
    <h2>关于我</h2>
    <div class="content">
      <p>
        你好！我是一名热爱前端开发的工程师。这个博客是用 <strong>Vue 3</strong> 构建的，旨在练习技术并记录学习过程。
      </p>
      <p>
        我喜欢钻研 JavaScript，也着迷于 UI 设计。我相信好的代码应该像诗歌一样优雅。
      </p>
    </div>
    
    <div class="social-links">
      <span class="link">GitHub</span>
      <span class="link">Email</span>
    </div>
  </div>
</template>

<style scoped>
.about-page {
  text-align: center;
  max-width: 600px;
  margin: 0 auto;
}

.profile-img {
  width: 100px;
  height: 100px;
  background-color: #2c3e50;
  color: white;
  border-radius: 50%;
  margin: 0 auto 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  font-family: 'Lora', serif;
}

h2 {
  font-family: 'Lora', serif;
  margin-bottom: 30px;
}

.content p {
  margin-bottom: 20px;
  text-align: left;
  color: #444;
}

.social-links {
  margin-top: 40px;
  display: flex;
  justify-content: center;
  gap: 20px;
}

.link {
  color: #999;
  cursor: pointer;
  font-size: 0.9rem;
  border-bottom: 1px solid transparent;
  transition: 0.3s;
}

.link:hover {
  color: #3e8e41;
  border-bottom-color: #3e8e41;
}
</style>