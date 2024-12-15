<template>
  <div class="container">
    <!-- GitHub 角标 -->
    <div class="github-corner">
      <a-button type="link" class="github-button" href="https://github.com/SeimoDev/Turn2Markdown" target="_blank">
        <a-space>
          <github-outlined style="font-size: 24px" />
          <span>Star on GitHub</span>
          <star-outlined style="font-size: 20px" />
        </a-space>
      </a-button>
    </div>
    <a-card class="upload-card" :bordered="false">
      <h1 class="title">Turn2Markdown</h1>
      <p class="description">一键将 Office 文档转换为 Markdown 格式<br><p style="color: red;">暂不支持图片内容</p></p>

      <a-space direction="vertical" size="large" style="width: 100%">
        <a-upload :before-upload="beforeUpload" :show-upload-list="false" accept=".docx,.pdf,.xlsx,.pptx">
          <a-button type="primary" size="large">
            <template #icon><upload-outlined /></template>
            选择文件
          </a-button>
        </a-upload>

        <div v-if="selectedFile" class="file-info">
          <a-space align="center">
            <file-outlined style="font-size: 20px" />
            <span style="max-width: 200px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;">
              {{ selectedFile.name }}
            </span>
            <a-button type="primary" @click="uploadFile" :loading="uploading">
              开始转换
            </a-button>
          </a-space>
        </div>

        <template v-if="conversionSuccess">
          <a-alert type="success" show-icon class="success-message">
            <template #message>
              <div class="success-content">
                <p>转换成功！文件将在 10 分钟后自动删除</p>
                <a-button type="primary" @click="downloadFile">
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

    <div class="github-link">
      <a-space>
        <github-outlined style="font-size: 20px" />
        <a href="https://github.com/microsoft/markitdown" target="_blank" rel="noopener noreferrer">
          Powered by Microsoft MarkItDown
        </a>
      </a-space>
    </div>
  </div>
</template>

<script>
import { UploadOutlined, FileOutlined, DownloadOutlined, GithubOutlined } from '@ant-design/icons-vue';
import axios from 'axios';

export default {
  components: {
    UploadOutlined,
    FileOutlined,
    DownloadOutlined,
    GithubOutlined,
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
    beforeUpload(file) {
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

    async uploadFile() {
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
        const response = await axios.post('http://127.0.0.1:8000/uploadfile/', formData, config);
        console.log('Response:', response.data);

        this.downloadUrl = `http://127.0.0.1:8000/output/${response.data.original_filename}.md`;
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

    downloadFile() {
      if (this.downloadUrl) {
        window.open(this.downloadUrl, '_blank');
      }
    }
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

.upload-card {
  width: 100%;
  max-width: 800px;
  text-align: center;
  margin: 0 auto;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.upload-card:hover {
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

@media (prefers-color-scheme: dark) {
  .upload-card {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  }

  .upload-card:hover {
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
  }
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

.file-info {
  margin: 24px 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.error-message {
  margin-top: 16px;
  width: 100%;
  max-width: 100%;
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

.github-link {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 8px 16px;
  background-color: rgba(255, 255, 255, 0.9);
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

@media (prefers-color-scheme: dark) {
  .github-link {
    background-color: rgba(0, 0, 0, 0.6);
  }

  .github-link a {
    color: #1890ff;
  }

  .github-link a:hover {
    color: #40a9ff;
  }
}

@media screen and (max-width: 576px) {
  .github-link {
    font-size: 12px;
    padding: 6px 12px;
    white-space: nowrap;
  }
}

/* 移动设备 - 小屏幕 */
@media screen and (max-width: 576px) {
  .container {
    padding: 8px;
  }

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

  .success-content {
    flex-direction: column;
    gap: 8px;
  }

  .success-content .ant-btn {
    width: 100%;
  }
}

/* 平板设备 - 中等屏幕 */
@media screen and (min-width: 577px) and (max-width: 992px) {
  .upload-card {
    max-width: 90%;
  }

  .title {
    font-size: 1.8rem;
  }
}

/* 桌面设备 - 大屏幕 */
@media screen and (min-width: 993px) {
  .container {
    padding: 24px;
  }

  .upload-card {
    padding: 32px;
  }

  .title {
    font-size: 2rem;
  }
}

/* 添加悬停效果 */
.ant-btn {
  transition: all 0.3s ease;
}

.ant-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

/* 文件信息样式 */
.file-info .ant-space {
  background: #f8f8f8;
  padding: 12px;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.file-info .ant-space:hover {
  background: #f0f0f0;
}

/* 添加暗色模式支持 */
@media (prefers-color-scheme: dark) {
  .file-info .ant-space {
    background: #262626;
  }

  .file-info .ant-space:hover {
    background: #303030;
  }
}
/* GitHub 角标样式 */
.github-corner {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 1000;
}

.github-button {
  background: rgba(255, 255, 255, 0.9);
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

@media (prefers-color-scheme: dark) {
  .github-button {
    background: rgba(0, 0, 0, 0.6);
    color: #fff;
  }
  
  .github-button:hover {
    color: #40a9ff;
  }
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