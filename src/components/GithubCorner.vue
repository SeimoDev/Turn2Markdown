<template>
  <div class="github-corner">
    <a-button type="link" class="github-button" href="https://github.com/SeimoDev/Turn2Markdown" target="_blank">
      <a-space>
        <github-outlined style="font-size: 24px" />
        <span>Star on GitHub</span>
        <star-outlined style="font-size: 20px" />
      </a-space>
    </a-button>
  </div>
</template>

<script>
import { GithubOutlined, StarOutlined } from '@ant-design/icons-vue';

export default {
  name: 'GithubCorner',
  components: {
    GithubOutlined,
    StarOutlined,
  },
};
</script>

<style scoped>
.github-corner {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 1000;
}

.github-button {
  background: #FFFFFF;
  border-radius: 24px;
  padding: 8px 16px;
  height: auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.github-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.github-button .anticon {
  transition: all 0.3s ease;
}

.github-button:hover .anticon-star {
  transform: scale(1.2);
  color: #ffeb3b;
}

[data-theme='dark'] .github-button {
  background: #262626;
  color: #fff;
}

[data-theme='dark'] .github-button:hover {
  color: #40a9ff;
}

@media screen and (max-width: 576px) {
  .github-corner {
    top: 10px;
    left: 10px;
  }

  .github-button {
    padding: 4px 12px;
  }

  .github-button span {
    font-size: 12px;
  }

  .github-button .anticon {
    font-size: 16px;
  }
}
</style> 