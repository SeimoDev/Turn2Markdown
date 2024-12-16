<template>
  <div class="github-link">
    <a-space>
      <github-outlined style="font-size: 20px" />
      <a href="https://github.com/microsoft/markitdown" target="_blank" rel="noopener noreferrer">
        Powered by Microsoft MarkItDown
      </a>
    </a-space>
  </div>
</template>

<script>
import { GithubOutlined } from '@ant-design/icons-vue';

export default {
  name: 'GithubLink',
  components: {
    GithubOutlined,
  },
};
</script>

<style scoped>
.github-link {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 8px 16px;
  background-color: #FFFFFF;
  border-radius: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.github-link:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transform: translateX(-50%) translateY(-2px);
}

.github-link a {
  color: #1890ff;
  text-decoration: none;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.github-link a:hover {
  color: #40a9ff;
}

[data-theme='dark'] .github-link {
  background-color: #262626;
}

@media screen and (max-width: 576px) {
  .github-link {
    font-size: 12px;
    padding: 6px 12px;
    white-space: nowrap;
  }
}
</style> 