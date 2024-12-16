<template>
  <a-config-provider :theme="{ algorithm: isDarkMode ? theme.darkAlgorithm : theme.defaultAlgorithm }">
    <div class="container">
      <ThemeSwitch v-model:isDarkMode="isDarkMode" />
      <GithubCorner />
      <UploadCard />
      <GithubLink />
    </div>
  </a-config-provider>
</template>

<script>
import { theme } from 'ant-design-vue';
import ThemeSwitch from './components/ThemeSwitch.vue';
import GithubCorner from './components/GithubCorner.vue';
import UploadCard from './components/UploadCard.vue';
import GithubLink from './components/GithubLink.vue';

export default {
  name: 'App',
  components: {
    ThemeSwitch,
    GithubCorner,
    UploadCard,
    GithubLink,
  },
  data() {
    return {
      isDarkMode: false,
      theme,
    };
  },
  watch: {
    isDarkMode: {
      handler(newValue) {
        document.documentElement.setAttribute('data-theme', newValue ? 'dark' : 'light');
      },
      immediate: true
    }
  },
  mounted() {
    // 检查系统主题偏好
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    this.isDarkMode = prefersDark;
    
    // 监听系统主题变化
    window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
      this.isDarkMode = e.matches;
    });
  },
};
</script>

<style>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 16px;
  box-sizing: border-box;
}

/* 添加按钮悬停效果 */
.ant-btn {
  transition: all 0.3s ease;
}

.ant-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

/* 响应式样式 */
@media screen and (max-width: 576px) {
  .container {
    padding: 8px;
  }
}

@media screen and (min-width: 993px) {
  .container {
    padding: 24px;
  }
}
</style>