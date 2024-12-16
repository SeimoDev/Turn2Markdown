<template>
  <div v-if="file" class="file-info">
    <a-space align="center">
      <file-outlined style="font-size: 20px" />
      <span style="max-width: 200px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;">
        {{ file.name }}
      </span>
      <a-button type="primary" @click="$emit('convert')" :loading="loading">
        开始转换
      </a-button>
    </a-space>
  </div>
</template>

<script>
import { FileOutlined } from '@ant-design/icons-vue';

export default {
  name: 'FileInfo',
  components: {
    FileOutlined,
  },
  props: {
    file: {
      type: File,
      default: null,
    },
    loading: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['convert'],
};
</script>

<style scoped>
.file-info {
  margin: 24px 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.file-info .ant-space {
  background: #FFFFFF;
  padding: 12px;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.file-info .ant-space:hover {
  background: #f0f0f0;
}

[data-theme='dark'] .file-info .ant-space {
  background: #262626;
}

[data-theme='dark'] .file-info .ant-space:hover {
  background: #303030;
}

@media screen and (max-width: 576px) {
  .file-info {
    flex-direction: column;
    gap: 8px;
  }

  .file-info .ant-space {
    flex-direction: column;
    width: 100%;
  }

  .file-info .ant-space-item {
    margin: 4px 0;
    width: 100%;
  }

  .file-info .ant-btn {
    width: 100%;
    margin-top: 8px;
  }
}
</style> 