<template>
  <a-card class="upload-card" :bordered="false">
    <h1 class="title">Turn2Markdown</h1>
    <p class="description">一键将 Office 文档转换为 Markdown 格式</p>
    <p style="color: red;">暂不支持图片内容</p>

    <a-space direction="vertical" size="large" style="width: 100%">
      <a-upload :before-upload="handleBeforeUpload" :show-upload-list="false" accept=".docx,.pdf,.xlsx,.pptx">
        <a-button type="primary" size="large">
          <template #icon><upload-outlined /></template>
          选择文件
        </a-button>
      </a-upload>

      <FileInfo 
        :file="selectedFile" 
        :loading="uploading"
        @convert="handleConvert"
      />

      <template v-if="conversionSuccess">
        <a-alert type="success" show-icon class="success-message">
          <template #message>
            <div class="success-content">
              <p>转换成功！文件将在 10 分钟后自动删除，请及时下载！</p>
              <a-button type="primary" @click="handleDownload">
                <template #icon><download-outlined /></template>
                下载 Markdown
              </a-button>
            </div>
          </template>
        </a-alert>
      </template>

      <a-alert v-if="errorMessage" :message="errorMessage" type="error" show-icon class="error-message" />
    </a-space>
  </a-card>
</template>

<script>
import { UploadOutlined, DownloadOutlined } from '@ant-design/icons-vue';
import axios from 'axios';
import FileInfo from './FileInfo.vue';

export default {
  name: 'UploadCard',
  components: {
    UploadOutlined,
    DownloadOutlined,
    FileInfo,
  },
  data() {
    return {
      selectedFile: null,
      errorMessage: '',
      uploading: false,
      conversionSuccess: false,
      downloadUrl: '',
    };
  },
  methods: {
    handleBeforeUpload(file) {
      const allowedTypes = ['.docx', '.pdf', '.xlsx', '.pptx'];
      const fileExtension = file.name.toLowerCase().substring(file.name.lastIndexOf('.'));

      if (!allowedTypes.includes(fileExtension)) {
        this.errorMessage = '只支持 docx、pdf、xlsx、pptx 格式的文件';
        return false;
      }

      this.selectedFile = file;
      this.errorMessage = '';
      this.conversionSuccess = false;
      return false;
    },

    async handleConvert() {
      if (!this.selectedFile) {
        this.errorMessage = '请先选择一个文件。';
        return;
      }

      const formData = new FormData();
      formData.append('file', this.selectedFile);
      this.uploading = true;
      this.conversionSuccess = false;
      this.errorMessage = '';

      try {
        const config = {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
          withCredentials: false
        };

        console.log('Sending request to server...');
        const response = await axios.post('https://mdapi.seimo.cn/uploadfile/', formData, config);
        console.log('Response:', response.data);

        this.downloadUrl = `https://mdapi.seimo.cn/output/${response.data.original_filename}.md`;
        this.conversionSuccess = true;
        this.errorMessage = '';
        this.selectedFile = null;
      } catch (error) {
        console.error('Upload error details:', {
          error: error,
          response: error.response,
          request: error.request,
          config: error.config
        });

        if (error.response) {
          const errorMessage = error.response.data.message || '服务器错误';
          switch (error.response.status) {
            case 400:
              this.errorMessage = `请求错误: ${errorMessage}`;
              break;
            case 404:
              this.errorMessage = `文件未找到: ${errorMessage}`;
              break;
            case 413:
              this.errorMessage = '文件太大，请选择小一点的文件';
              break;
            case 415:
              this.errorMessage = '不支持的文件类型';
              break;
            case 422:
              this.errorMessage = `文件验证失败: ${errorMessage}`;
              break;
            case 500:
              this.errorMessage = '服务器内部错误，请稍后重试';
              break;
            default:
              this.errorMessage = `上传失败: ${errorMessage}`;
          }
        } else if (error.request) {
          console.log('No response received:', error.request);
          this.errorMessage = '跨域请求被阻止，请在后端添加 CORS 支持';
        } else {
          this.errorMessage = '上传过程中发生错误，请重试';
        }
      } finally {
        this.uploading = false;
      }
    },

    handleDownload() {
      if (this.downloadUrl) {
        window.open(this.downloadUrl, '_blank');
      }
    },
  },
};
</script>

<style scoped>
.upload-card {
  width: 100%;
  max-width: 800px;
  text-align: center;
  margin: 0 auto;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  background-color: #FFFFFF;
}

.upload-card:hover {
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
}

.title {
  color: #1890ff;
  margin-bottom: 16px;
  font-weight: 600;
  transition: font-size 0.3s ease;
}

.description {
  color: #666;
  margin-bottom: 32px;
  line-height: 1.5;
}

.success-message {
  margin-top: 16px;
}

.success-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.success-content p {
  margin: 0;
  font-size: 14px;
}

.error-message {
  margin-top: 16px;
  width: 100%;
  max-width: 100%;
}

[data-theme='dark'] .upload-card {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  background-color: #262626;
}

[data-theme='dark'] .upload-card:hover {
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
}

@media screen and (max-width: 576px) {
  .upload-card {
    max-width: 100%;
  }

  .title {
    font-size: 1.5rem;
  }

  .description {
    font-size: 0.9rem;
    padding: 0 16px;
  }

  .success-content {
    flex-direction: column;
    gap: 8px;
  }

  .success-content .ant-btn {
    width: 100%;
  }
}

@media screen and (min-width: 577px) and (max-width: 992px) {
  .upload-card {
    max-width: 90%;
  }

  .title {
    font-size: 1.8rem;
  }
}

@media screen and (min-width: 993px) {
  .upload-card {
    padding: 32px;
  }

  .title {
    font-size: 2rem;
  }
}
</style> 