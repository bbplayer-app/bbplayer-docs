<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import { Play, Tv2, AlertCircle, Music2 } from "lucide-vue-next";

const id = ref("");
const title = ref("");
const cover = ref("");
const error = ref("");
const bvid = ref("");
const cid = ref("");

onMounted(() => {
  const params = new URLSearchParams(window.location.search);
  id.value = params.get("id") || "";
  title.value = params.get("title") || "";
  cover.value = params.get("cover") || "";

  // Parse bilibili id
  if (id.value.startsWith("bilibili::")) {
    const parts = id.value.split("::");
    if (parts.length >= 2) {
      bvid.value = parts[1];
      if (parts.length >= 3) {
        cid.value = parts[2];
      }
    } else {
      error.value = "无效的分享链接";
    }
  } else {
    error.value = "暂不支持此来源的分享链接";
  }
});

const bilibiliUrl = computed(() => {
  if (!bvid.value) return "";
  let url = `https://www.bilibili.com/video/${bvid.value}`;
  if (cid.value) {
    url += `?p=1`;
  }
  return url;
});

const bbplayerUrl = computed(() => {
  if (!bvid.value) return "";
  return `https://bbplayer.roitium.com/app/link-to/playlist/remote/multipage/${bvid.value}`;
});
</script>

<template>
  <div class="share-track-container">
    <div class="share-card" v-if="!error">
      <div class="cover-wrapper">
        <img
          v-if="cover"
          :src="cover"
          :alt="title"
          class="cover-image"
          referrerpolicy="no-referrer"
        />
        <div v-else class="cover-placeholder">
          <Music2 :size="64" color="#9e9e9e" />
        </div>
      </div>

      <h1 class="track-title">{{ title || "未知曲目" }}</h1>

      <div class="button-group">
        <a
          :href="bilibiliUrl"
          target="_blank"
          rel="noopener noreferrer"
          class="btn btn-bilibili"
        >
          <Tv2 class="btn-icon" :size="22" />
          在 Bilibili 打开
        </a>

        <a :href="bbplayerUrl" class="btn btn-bbplayer">
          <Play class="btn-icon" :size="22" fill="currentColor" />
          在 BBPlayer 打开
        </a>
      </div>

      <p class="hint">BBPlayer 是一个开源的 B 站音乐播放器</p>
    </div>

    <div class="error-card" v-else>
      <div class="error-icon">
        <AlertCircle :size="64" />
      </div>
      <h2 class="error-title">{{ error }}</h2>
      <p class="error-desc">请检查分享链接是否正确</p>
    </div>
  </div>
</template>

<style scoped>
.share-track-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  font-family:
    -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue",
    Arial, sans-serif;
}

.share-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 24px;
  padding: 40px;
  max-width: 420px;
  width: 100%;
  text-align: center;
  box-shadow:
    0 25px 50px -12px rgba(0, 0, 0, 0.25),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.cover-wrapper {
  width: 200px;
  height: 200px;
  margin: 0 auto 24px;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
}

.cover-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.cover-image:hover {
  transform: scale(1.05);
}

.cover-placeholder {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #e0e0e0 0%, #bdbdbd 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.track-title {
  font-size: 1.5rem;
  font-weight: 700;
  color: #1a1a2e;
  margin-bottom: 32px;
  line-height: 1.4;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 16px 24px;
  border-radius: 14px;
  font-size: 1rem;
  font-weight: 600;
  text-decoration: none;
  transition: all 0.2s ease;
  cursor: pointer;
}

.btn-icon {
  width: 22px;
  height: 22px;
}

.btn-bilibili {
  background: linear-gradient(135deg, #fb7299 0%, #f25d8e 100%);
  color: white;
}

.btn-bilibili:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(251, 114, 153, 0.4);
}

.btn-bbplayer {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.btn-bbplayer:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.4);
}

.hint {
  margin-top: 24px;
  font-size: 0.875rem;
  color: #6b7280;
}

.error-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 24px;
  padding: 48px 40px;
  max-width: 400px;
  width: 100%;
  text-align: center;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  animation: slideUp 0.5s ease-out;
}

.error-icon {
  margin: 0 auto 20px;
  color: #ef4444;
  display: flex;
  justify-content: center;
}

.error-title {
  font-size: 1.25rem;
  font-weight: 600;
  color: #1f2937;
  margin-bottom: 8px;
}

.error-desc {
  font-size: 0.9rem;
  color: #6b7280;
}

@media (max-width: 480px) {
  .share-card {
    padding: 32px 24px;
  }

  .cover-wrapper {
    width: 160px;
    height: 160px;
  }

  .track-title {
    font-size: 1.25rem;
  }
}
</style>
