<template>
  <div class="home-wrapper">
    <div class="intro">
      <span class="tag">👋 Welcome</span>
      <h1 class="hero-title">
        基于Vue 3构建的个人博客系统。
      </h1>
      <p class="subtitle">
        这里是我的博客。
      </p>
      <div class="actions">
        <button class="btn-primary" @click="$router.push('/blog')">开始阅读</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.home-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 60vh; 
}
.intro {
  max-width: 600px;
}
.tag {
  background: #e8f5e9;
  color: #2e7d32;
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 700;
  display: inline-block;
  margin-bottom: 20px;
}
.hero-title {
  font-family: 'Lora', serif;
  font-size: 3rem;
  line-height: 1.2;
  margin-bottom: 20px;
  color: #1a1a1a;
}
.subtitle {
  color: #666;
  font-size: 1.1rem;
  margin-bottom: 30px;
}
.btn-primary {
  background-color: #2c3e50;
  color: #fff;
  border: none;
  padding: 12px 30px;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s;
}
.btn-primary:hover {
  background-color: #3e8e41;
  transform: translateY(-2px);
}
</style>