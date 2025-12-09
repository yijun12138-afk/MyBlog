<template>
  <div class="home-wrapper">
    <div class="intro">
      <span class="tag">👋 Welcome</span>
      <h1 class="hero-title">
        记录生活，<br>
        探索代码的诗意。
      </h1>
      <p class="subtitle">
        这里是我的数字花园。分享关于技术、设计与生活的思考。
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
  height: 60vh; /* 占据屏幕高度的60% */
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
  background-color: #3e8e41; /* 悬停变绿 */
  transform: translateY(-2px);
}
</style>