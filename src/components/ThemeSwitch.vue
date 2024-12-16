<template>
  <div class="theme-switch">
    <a-button type="link" @click="toggleTheme">
      <template #icon>
        <BulbOutlined v-if="isDarkMode" />
        <BulbFilled v-else />
      </template>
    </a-button>
  </div>
</template>

<script>
import { BulbOutlined, BulbFilled } from '@ant-design/icons-vue';

export default {
  name: 'ThemeSwitch',
  components: {
    BulbOutlined,
    BulbFilled,
  },
  props: {
    isDarkMode: {
      type: Boolean,
      required: true,
    },
  },
  emits: ['update:isDarkMode'],
  methods: {
    toggleTheme() {
      this.$emit('update:isDarkMode', !this.isDarkMode);
    },
  },
};
</script>

<style scoped>
.theme-switch {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 1000;
}

.theme-switch .ant-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #FFFFFF;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

[data-theme='dark'] .theme-switch .ant-btn {
  background: #262626;
}

@media screen and (max-width: 576px) {
  .theme-switch {
    top: 10px;
    right: 10px;
  }
  
  .theme-switch .ant-btn {
    width: 32px;
    height: 32px;
  }
}
</style> 