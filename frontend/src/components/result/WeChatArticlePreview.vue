<template>
  <div class="preview-container">
    <div class="preview-header">
      <h2 class="preview-title">微信公众号图文预览</h2>
      <div class="preview-actions">
        <button class="btn btn-primary" @click="exportImages">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
            <polyline points="7 10 12 15 17 10"></polyline>
            <line x1="12" y1="15" x2="12" y2="3"></line>
          </svg>
          导出全部图片
        </button>
        <button class="btn" @click="copyAllImages" style="border: 1px solid var(--border-color);">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
            <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
          </svg>
          复制全部
        </button>
      </div>
    </div>

    <div class="preview-content">
      <div class="preview-sidebar">
        <h3 class="sidebar-title">页面列表</h3>
        <div class="page-list">
          <div
            v-for="(page, index) in pages"
            :key="index"
            class="page-item"
            :class="{ active: currentPage === index }"
            @click="currentPage = index"
          >
            <div class="page-thumbnail">
              <img v-if="getImageUrl(index)" :src="getImageUrl(index)" :alt="`Page ${index + 1}`" />
              <div v-else class="thumbnail-placeholder">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                  <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
                  <circle cx="8.5" cy="8.5" r="1.5"></circle>
                  <polyline points="21 15 16 10 5 21"></polyline>
                </svg>
              </div>
            </div>
            <div class="page-info">
              <span class="page-type-badge" :class="page.type">{{ getPageTypeText(page.type) }}</span>
              <span class="page-number">Page {{ index + 1 }}</span>
            </div>
          </div>
        </div>
      </div>

      <div class="preview-main">
        <div class="phone-frame">
          <div class="phone-screen">
            <div class="wechat-article">
              <div class="article-header">
                <div class="article-cover" v-if="getImageUrl(currentPage)">
                  <img :src="getImageUrl(currentPage)" :alt="`Page ${currentPage + 1}`" />
                </div>
                <div class="article-meta">
                  <span class="meta-label">原创</span>
                </div>
              </div>

              <div class="article-content" v-html="formatContent(currentPageContent)"></div>

              <div class="article-footer">
                <div class="read-count">
                  <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                    <circle cx="12" cy="12" r="3"></circle>
                  </svg>
                  <span>阅读 10W+</span>
                </div>
                <div class="like-count">
                  <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="2">
                    <path d="M14 9V5a3 3 0 0 0-3-3l-4 9v11h11.28a2 2 0 0 0 2-1.7l1.38-9a2 2 0 0 0-2-2.3zM7 22H4a2 2 0 0 1-2-2v-7a2 2 0 0 1 2-2h3"></path>
                  </svg>
                  <span>999+</span>
                </div>
              </div>

              <div class="article-copyright">
                <div class="copyright-icon">
                  <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                    <circle cx="12" cy="12" r="10"></circle>
                    <line x1="14" y1="12" x2="14" y2="12"></line>
                    <line x1="12" y1="12" x2="12" y2="12"></line>
                    <circle cx="12" cy="12" r="3"></circle>
                  </svg>
                </div>
                <div class="copyright-text">
                  <p>未经授权，不得转载</p>
                </div>
              </div>
            </div>
          </div>

          <div class="phone-nav">
            <button
              class="nav-btn"
              :disabled="currentPage === 0"
              @click="prevPage"
            >
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <polyline points="15 18 9 12 15 6"></polyline>
              </svg>
              上一页
            </button>
            <span class="page-indicator">{{ currentPage + 1 }} / {{ pages.length }}</span>
            <button
              class="nav-btn"
              :disabled="currentPage === pages.length - 1"
              @click="nextPage"
            >
              下一页
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <polyline points="9 18 15 12 9 6"></polyline>
              </svg>
            </button>
          </div>
        </div>

        <div class="page-detail">
          <h3 class="detail-title">页面详情</h3>
          <div class="detail-content">
            <div class="detail-item">
              <label>页面类型</label>
              <span class="page-type-badge large" :class="currentPageData?.type">
                {{ getPageTypeText(currentPageData?.type) }}
              </span>
            </div>
            <div class="detail-item">
              <label>页面内容</label>
              <div class="content-preview">{{ currentPageData?.content || '暂无内容' }}</div>
            </div>
            <div class="detail-item">
              <label>图片状态</label>
              <span class="status-badge" :class="getImageStatus(currentPage)">
                {{ getImageStatusText(currentPage) }}
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useGeneratorStore } from '../../stores/generator'

interface Page {
  index: number
  type: 'cover' | 'content' | 'summary'
  content: string
}

const props = defineProps<{
  pages: Page[]
}>()

const store = useGeneratorStore()
const currentPage = ref(0)

const currentPageData = computed(() => props.pages[currentPage.value])

const currentPageContent = computed(() => {
  return currentPageData.value?.content || ''
})

function getImageUrl(index: number): string | undefined {
  const image = store.images.find(img => img.index === index)
  if (image && image.url && image.status === 'done') {
    return image.url.split('?')[0] + '?thumbnail=false'
  }
  return undefined
}

function getImageStatus(index: number): string {
  const image = store.images.find(img => img.index === index)
  if (!image) return 'pending'
  return image.status
}

function getImageStatusText(index: number): string {
  const status = getImageStatus(index)
  const texts: Record<string, string> = {
    generating: '生成中',
    done: '已完成',
    error: '失败',
    retrying: '重试中',
    pending: '等待中'
  }
  return texts[status] || '未知'
}

function getPageTypeText(type?: string): string {
  const texts: Record<string, string> = {
    cover: '封面',
    content: '正文',
    summary: '总结'
  }
  return texts[type || ''] || '未知'
}

function formatContent(content: string): string {
  if (!content) return '<p class="empty-content">暂无内容</p>'
  
  return content
    .split('\n')
    .filter(line => line.trim())
    .map(line => `<p>${line}</p>`)
    .join('')
}

function prevPage() {
  if (currentPage.value > 0) {
    currentPage.value--
  }
}

function nextPage() {
  if (currentPage.value < props.pages.length - 1) {
    currentPage.value++
  }
}

async function exportImages() {
  for (let i = 0; i < props.pages.length; i++) {
    const url = getImageUrl(i)
    if (url) {
      try {
        const response = await fetch(url)
        const blob = await response.blob()
        const link = document.createElement('a')
        link.href = URL.createObjectURL(blob)
        link.download = `wechat-article-page-${i + 1}.png`
        link.click()
        URL.revokeObjectURL(link.href)
      } catch (error) {
        console.error(`导出第 ${i + 1} 页失败:`, error)
      }
    }
  }
}

async function copyAllImages() {
  for (let i = 0; i < props.pages.length; i++) {
    const url = getImageUrl(i)
    if (url) {
      try {
        const response = await fetch(url)
        const blob = await response.blob()
        await navigator.clipboard.write([
          new ClipboardItem({
            [blob.type]: blob
          })
        ])
        console.log(`已复制第 ${i + 1} 页`)
      } catch (error) {
        console.error(`复制第 ${i + 1} 页失败:`, error)
      }
    }
  }
  alert('图片已复制到剪贴板')
}
</script>

<style scoped>
.preview-container {
  background: #f5f5f5;
  border-radius: 12px;
  overflow: hidden;
}

.preview-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  background: white;
  border-bottom: 1px solid var(--border-color);
}

.preview-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-main);
  margin: 0;
}

.preview-actions {
  display: flex;
  gap: 12px;
}

.preview-actions .btn {
  display: flex;
  align-items: center;
  gap: 6px;
}

.preview-content {
  display: flex;
  min-height: 600px;
}

.preview-sidebar {
  width: 240px;
  background: white;
  border-right: 1px solid var(--border-color);
  padding: 16px;
  overflow-y: auto;
}

.sidebar-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-main);
  margin: 0 0 16px 0;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-color);
}

.page-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.page-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s;
  border: 2px solid transparent;
}

.page-item:hover {
  background: #f5f5f5;
}

.page-item.active {
  background: var(--primary-light);
  border-color: var(--primary);
}

.page-thumbnail {
  width: 50px;
  height: 67px;
  border-radius: 4px;
  overflow: hidden;
  flex-shrink: 0;
}

.page-thumbnail img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.thumbnail-placeholder {
  width: 100%;
  height: 100%;
  background: #f0f0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text-placeholder);
}

.page-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
  min-width: 0;
}

.page-type-badge {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 500;
  text-align: center;
}

.page-type-badge.cover {
  background: #FFF7E6;
  color: #FA8C16;
}

.page-type-badge.content {
  background: #E6F7FF;
  color: #1890FF;
}

.page-type-badge.summary {
  background: #F6FFED;
  color: #52C41A;
}

.page-type-badge.large {
  padding: 4px 12px;
  font-size: 13px;
}

.page-number {
  font-size: 12px;
  color: var(--text-sub);
}

.preview-main {
  flex: 1;
  display: flex;
  padding: 24px;
  gap: 24px;
}

.phone-frame {
  width: 375px;
  background: #1a1a1a;
  border-radius: 40px;
  padding: 12px;
  flex-shrink: 0;
}

.phone-screen {
  background: white;
  border-radius: 32px;
  overflow: hidden;
  height: 600px;
  overflow-y: auto;
}

.phone-nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 8px 8px;
}

.nav-btn {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 8px 12px;
  background: transparent;
  border: none;
  color: white;
  font-size: 13px;
  cursor: pointer;
  transition: all 0.2s;
}

.nav-btn:hover:not(:disabled) {
  color: var(--primary);
}

.nav-btn:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.page-indicator {
  color: #888;
  font-size: 13px;
}

.wechat-article {
  padding: 0;
}

.article-header {
  position: relative;
}

.article-cover {
  width: 100%;
  aspect-ratio: 2.35/1;
  overflow: hidden;
}

.article-cover img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.article-meta {
  padding: 12px 16px;
  display: flex;
  justify-content: flex-end;
}

.meta-label {
  font-size: 12px;
  color: var(--text-sub);
}

.article-content {
  padding: 20px 16px;
  font-size: 16px;
  line-height: 1.8;
  color: #333;
  min-height: 200px;
}

.article-content :deep(p) {
  margin: 0 0 16px 0;
  text-align: justify;
}

.article-content :deep(.empty-content) {
  color: var(--text-placeholder);
  text-align: center;
}

.article-footer {
  padding: 16px;
  display: flex;
  justify-content: center;
  gap: 48px;
  border-top: 1px solid #f0f0f0;
}

.read-count,
.like-count {
  display: flex;
  align-items: center;
  gap: 6px;
  color: var(--text-sub);
  font-size: 13px;
}

.article-copyright {
  padding: 16px;
  display: flex;
  align-items: flex-start;
  gap: 8px;
}

.copyright-icon {
  color: var(--text-placeholder);
  flex-shrink: 0;
}

.copyright-text p {
  margin: 0;
  font-size: 12px;
  color: var(--text-placeholder);
}

.page-detail {
  flex: 1;
  background: white;
  border-radius: 12px;
  padding: 20px;
  height: fit-content;
}

.detail-title {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-main);
  margin: 0 0 16px 0;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-color);
}

.detail-content {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.detail-item label {
  font-size: 13px;
  color: var(--text-sub);
  font-weight: 500;
}

.content-preview {
  padding: 12px;
  background: #f9f9f9;
  border-radius: 8px;
  font-size: 14px;
  color: var(--text-main);
  line-height: 1.6;
  max-height: 150px;
  overflow-y: auto;
  white-space: pre-wrap;
  word-break: break-word;
}

.status-badge {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: 500;
}

.status-badge.done {
  background: #E6F7ED;
  color: #52C41A;
}

.status-badge.generating,
.status-badge.retrying {
  background: #E6F4FF;
  color: #1890FF;
}

.status-badge.error {
  background: #FFF1F0;
  color: #FF4D4F;
}

.status-badge.pending {
  background: #F5F5F5;
  color: #999;
}

.btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 10px 16px;
  border-radius: var(--radius-md);
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
  border: none;
  background: white;
  color: var(--text-main);
  border: 1px solid var(--border-color);
}

.btn:hover {
  background: #f5f5f5;
}

.btn-primary {
  background: var(--primary);
  color: white;
  border-color: var(--primary);
}

.btn-primary:hover {
  background: var(--primary-hover);
}
</style>
