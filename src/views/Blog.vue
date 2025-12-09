<template>
  <div class="blog-wrapper">
    <div class="controls">
      <div class="search-box">
        <i class="search-icon">🔍</i>
        <input
          v-model="searchQuery"
          type="text"
          placeholder="搜索文章标题..."
        />
      </div>

      <div class="action-buttons">
        <transition name="fade">
          <button
            v-if="selectedIds.length > 0"
            class="btn-batch-delete"
            @click="batchDelete"
          >
            删除选中 ({{ selectedIds.length }})
          </button>
        </transition>
        <button class="btn-create" @click="showCreateModal = true">
          + 发布文章
        </button>
      </div>
    </div>
    <div v-if="filteredPosts.length > 0" class="selection-bar">
      <label class="checkbox-label">
        <input
          type="checkbox"
          :checked="isAllSelected"
          @change="toggleSelectAll"
        />
        <span>全选当前列表</span>
      </label>
    </div>
    <div v-if="loading" class="loading-state">
      <div class="spinner"></div>
      正在从云端加载数据...
    </div>
    <div v-else class="post-list">
      <transition-group name="list">
        <article
          v-for="post in filteredPosts"
          :key="post.id"
          class="post-card"
          :class="{ 'is-selected': selectedIds.includes(post.id) }"
        >
          <div class="checkbox-wrapper">
            <input type="checkbox" :value="post.id" v-model="selectedIds" />
          </div>
          <div class="card-content">
            <h2 class="post-title" @click="goToDetail(post.id)">
              {{ post.title }}
            </h2>
            <p class="post-excerpt">{{ post.body.substring(0, 80) }}...</p>
            <div class="post-meta">
              <span>作者: {{ post.userId }}</span>
              <button class="btn-delete" @click="deletePost(post.id)">
                删除
              </button>
            </div>
          </div>
        </article>
      </transition-group>
      <!-- 空状态提示 -->
      <div v-if="filteredPosts.length === 0" class="empty-state">
        没有找到相关文章。
      </div>
    </div>
    <div v-if="showCreateModal" class="modal-overlay">
      <div class="modal-content">
        <h3>撰写新文章</h3>
        <input
          v-model="newPost.title"
          placeholder="请输入标题"
          class="modal-input"
        />
        <textarea
          v-model="newPost.body"
          placeholder="请输入正文内容..."
          rows="5"
          class="modal-input"
        ></textarea>
        <div class="modal-actions">
          <button @click="showCreateModal = false" class="btn-cancel">
            取消
          </button>
          <button @click="createPost" class="btn-confirm">发布</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

// --- 状态定义 ---
const router = useRouter();
const posts = ref([]); // 文章列表数据
const loading = ref(true); // 加载状态
const searchQuery = ref(""); // 搜索词
const showCreateModal = ref(false); // 弹窗显示状态
const newPost = ref({ title: "", body: "" }); // 新文章表单

// 【核心状态】存放所有被勾选的文章 ID
const selectedIds = ref([]);

const API = "https://jsonplaceholder.typicode.com/posts";

// --- 1. 获取数据 (Read) ---
const fetchPosts = async () => {
  try {
    const res = await axios.get(API);
    // 数据太多只选了前十五个
    posts.value = res.data.slice(0, 15);
  } catch (error) {
    alert("数据加载失败！");
  } finally {
    loading.value = false;// 无论成功失败，最后都要把加载动画关掉
  }
};

// --- 2. 搜索过滤 (Computed) ---
const filteredPosts = computed(() => {
  return posts.value.filter((post) =>
    post.title.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

// --- 3. 全选逻辑 (Computed & Methods) ---
// 判断当前筛选出的列表是否全部被选中
const isAllSelected = computed(() => {
  if (filteredPosts.value.length === 0) return false;
  return filteredPosts.value.every((post) =>
    selectedIds.value.includes(post.id)
  );
});
const toggleSelectAll = () => {
  if (isAllSelected.value) {
    selectedIds.value = [];
  } else {
    selectedIds.value = filteredPosts.value.map((p) => p.id);
  }
};

// --- 4. 批量删除逻辑 (Batch Delete) ---
//JSONPlaceholder 这个公共测试接口,只提供了基于单个 ID 的 DELETE /posts/:id 接口
const batchDelete = async () => {
  const count = selectedIds.value.length;
  if (!confirm(`确定要删除选中的 ${count} 篇文章吗？`)) return;
  try {
    const deletePromises = selectedIds.value.map((id) =>
      axios.delete(`${API}/${id}`)
    );
    await Promise.all(deletePromises);
    posts.value = posts.value.filter(
      (post) => !selectedIds.value.includes(post.id)
    );
    selectedIds.value = [];
    alert(`成功批量删除 ${count} 篇文章`);
  } catch (error) {
    console.error(error);
    alert("批量删除失败");
  }
};

// --- 5. 单个删除逻辑 (Delete) ---
const deletePost = async (id) => {
  if (!confirm("确定要删除这篇文章吗？")) return;

  try {
    await axios.delete(`${API}/${id}`);

    // 从列表移除
    posts.value = posts.value.filter((p) => p.id !== id);
    // 如果删除的刚好是被选中的，也要从 selectedIds 里移除，防止计数错误
    selectedIds.value = selectedIds.value.filter((sid) => sid !== id);
  } catch (error) {
    alert("删除失败");
  }
};

// --- 6. 发布文章逻辑 (Create) ---
const createPost = async () => {
  if (!newPost.value.title || !newPost.value.body) return alert("请填写完整");

  try {
    const res = await axios.post(API, {
      title: newPost.value.title,
      body: newPost.value.body,
      userId: 1,
    });

    // 【重要】因为是假接口，服务器返回的ID总是101。
    // 为了防止前端列表里的ID重复（导致删除出错），我们用 Date.now() 造一个假的时间戳ID。
    //因为 JSONPlaceholder 无论发布什么，返回的 ID 都是 101。如果在列表里有多个 ID 为 101 的文章，
    // Vue 的渲染 (v-for key) 会报错，删除功能也会乱套（删一个把其他的也删了）。
    // 用时间戳可以保证 ID 唯一
    const fPost = { ...res.data, id: Date.now() };

     // unshift 把新文章加到数组的最前面
    posts.value.unshift(fPost);

    // 关闭弹窗并清空
    showCreateModal.value = false;
    newPost.value = { title: "", body: "" };
    alert("发布成功");
  } catch (error) {
    alert("发布失败");
  }
};

// --- 7. 跳转详情页 ---
const goToDetail = (id) => {
  router.push(`/blog/${id}`);
};

// 页面加载时自动拉取数据
onMounted(fetchPosts);
</script>

<style scoped>
.blog-wrapper {
  max-width: 800px;
  margin: 0 auto;
  padding-bottom: 50px;
}
.controls {
  display: flex;
  gap: 15px;
  margin-bottom: 20px;
  align-items: center;
}
.search-box {
  flex: 1;
  position: relative;
  display: flex;
  align-items: center;
}
.search-icon {
  position: absolute;
  left: 15px;
  font-style: normal;
  opacity: 0.5;
}
.search-box input {
  width: 100%;
  padding: 12px 12px 12px 40px;
  border: 1px solid #ddd;
  border-radius: 50px;
  outline: none;
  transition: all 0.3s;
  font-family: inherit;
  background: white;
}
.search-box input:focus {
  border-color: #3e8e41;
  box-shadow: 0 0 0 3px rgba(62, 142, 65, 0.1);
}

/* 按钮组 */
.action-buttons {
  display: flex;
  gap: 10px;
}

.btn-create {
  background: #2c3e50;
  color: white;
  border: none;
  padding: 0 20px;
  height: 40px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: bold;
  transition: 0.3s;
  white-space: nowrap;
}

.btn-create:hover {
  background: #3e8e41;
}

/* 红色批量删除按钮 */
.btn-batch-delete {
  background: #e53935;
  color: white;
  border: none;
  padding: 0 20px;
  height: 40px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: bold;
  box-shadow: 0 4px 10px rgba(229, 57, 53, 0.3);
  transition: 0.3s;
  white-space: nowrap;
}

.btn-batch-delete:hover {
  background: #c62828;
}

/* 全选控制栏 */
.selection-bar {
  margin-bottom: 15px;
  padding-left: 10px;
  color: #555;
  user-select: none;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.9rem;
}

.checkbox-label input {
  width: 18px;
  height: 18px;
  accent-color: #2c3e50;
  cursor: pointer;
}

/* 加载状态 */
.loading-state {
  text-align: center;
  padding: 50px;
  color: #999;
}

.post-card {
  background: white;
  padding: 20px;
  margin-bottom: 15px;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.03);
  border: 1px solid rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
  display: flex; 
  align-items: flex-start;
}
.post-card.is-selected {
  background-color: #f1f8e9;
  border-color: #3e8e41;
  transform: translateX(5px);
}
.post-card:hover {
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.08);
}

/* 复选框包裹层 */
.checkbox-wrapper {
  margin-right: 15px;
  padding-top: 5px; /* 对齐标题 */
}

.checkbox-wrapper input {
  width: 20px;
  height: 20px;
  cursor: pointer;
  accent-color: #3e8e41; /* 选中时的颜色 */
}

/* 卡片内容区 */
.card-content {
  flex: 1; /* 占满剩余空间 */
}

.post-title {
  font-family: "Lora", serif;
  cursor: pointer;
  color: #2c3e50;
  margin: 0 0 10px 0;
  font-size: 1.4rem;
  line-height: 1.3;
}

.post-title:hover {
  color: #3e8e41;
  text-decoration: underline;
}

.post-excerpt {
  color: #666;
  font-size: 0.95rem;
  margin-bottom: 15px;
}

.post-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.85rem;
  color: #999;
}

.btn-delete {
  background: #ffebee;
  color: #c62828;
  border: none;
  padding: 5px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.8rem;
  transition: 0.2s;
}

.btn-delete:hover {
  background: #c62828;
  color: white;
}

/* 列表动画 */
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* 按钮淡入淡出动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 100;
  backdrop-filter: blur(5px);
}

.modal-content {
  background: white;
  padding: 30px;
  border-radius: 15px;
  width: 90%;
  max-width: 500px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.2);
}

.modal-content h3 {
  margin-bottom: 20px;
  color: #2c3e50;
}

.modal-input {
  width: 100%;
  margin-bottom: 15px;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-family: inherit;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.btn-confirm {
  background: #2c3e50;
  color: white;
  border: none;
  padding: 10px 25px;
  border-radius: 6px;
  cursor: pointer;
}
.btn-cancel {
  background: #f5f5f5;
  color: #666;
  border: none;
  padding: 10px 25px;
  border-radius: 6px;
  cursor: pointer;
}
</style>
