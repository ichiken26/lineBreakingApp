<template>
  <div class="app">
    <div class="container">
      <h1>改行 → &lt;br&gt; 変換</h1>
      <p class="description">
        テキストを貼り付けると、改行をすべて <code>&lt;br&gt;</code> に置換します。
      </p>

      <div class="grid">
        <section class="panel">
          <div class="panel-header">
            <h2>入力</h2>
            <button class="sub-button" @click="clearText" :disabled="!inputText">
              クリア
            </button>
          </div>
          <textarea
            v-model="inputText"
            class="textarea"
            placeholder="ここにテキストを貼り付けてください"
          />
        </section>

        <section class="panel">
          <div class="panel-header">
            <h2>変換結果</h2>
            <button class="sub-button" @click="copyResult" :disabled="!convertedText">
              {{ copied ? 'コピー済み' : 'コピー' }}
            </button>
          </div>
          <textarea
            :value="convertedText"
            class="textarea output"
            readonly
            placeholder="ここに変換結果が表示されます"
          />
        </section>
      </div>

      <section class="preview-panel">
        <div class="panel-header">
          <h2>プレビュー</h2>
        </div>
        <div class="preview" v-html="previewHtml"></div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const inputText = ref('')
const copied = ref(false)

const convertedText = computed(() => {
  return inputText.value.replace(/\r\n|\r|\n/g, '<br>')
})

const escapeHtml = (text) => {
  return text
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#039;')
}

const previewHtml = computed(() => {
  return escapeHtml(inputText.value).replace(/\r\n|\r|\n/g, '<br>')
})

const copyResult = async () => {
  try {
    await navigator.clipboard.writeText(convertedText.value)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 1500)
  } catch (error) {
    console.error('コピーに失敗しました', error)
    alert('コピーに失敗しました。手動でコピーしてください。')
  }
}

const clearText = () => {
  inputText.value = ''
}

watch(inputText, () => {
  copied.value = false
})
</script>

<style scoped>
:global(*) {
  box-sizing: border-box;
}

:global(body) {
  margin: 0;
  font-family: Inter, "Hiragino Sans", "Hiragino Kaku Gothic ProN", "Noto Sans JP", sans-serif;
  background: #f5f7fb;
  color: #1f2937;
}

.app {
  min-height: 100vh;
  padding: 32px 16px;
}

.container {
  max-width: 1100px;
  margin: 0 auto;
}

h1 {
  margin: 0 0 8px;
  font-size: 32px;
}

.description {
  margin: 0 0 24px;
  color: #4b5563;
  line-height: 1.6;
}

code {
  background: #e5e7eb;
  padding: 2px 6px;
  border-radius: 6px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 20px;
  margin-bottom: 20px;
}

.panel,
.preview-panel {
  background: #ffffff;
  border-radius: 16px;
  padding: 18px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 12px;
}

.panel-header h2 {
  margin: 0;
  font-size: 18px;
}

.textarea {
  width: 100%;
  min-height: 320px;
  resize: vertical;
  border: 1px solid #d1d5db;
  border-radius: 12px;
  padding: 14px;
  font-size: 14px;
  line-height: 1.7;
  background: #fcfcfd;
}

.textarea:focus {
  outline: none;
  border-color: #6366f1;
  box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.15);
}

.output {
  background: #f9fafb;
}

.sub-button {
  border: none;
  background: #111827;
  color: white;
  padding: 10px 14px;
  border-radius: 10px;
  font-size: 14px;
  cursor: pointer;
  transition: transform 0.15s ease, opacity 0.15s ease;
}

.sub-button:hover:not(:disabled) {
  transform: translateY(-1px);
  opacity: 0.92;
}

.sub-button:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.preview {
  min-height: 140px;
  padding: 16px;
  border: 1px solid #d1d5db;
  border-radius: 12px;
  background: #fcfcfd;
  line-height: 1.8;
  white-space: normal;
  word-break: break-word;
}

@media (max-width: 800px) {
  .grid {
    grid-template-columns: 1fr;
  }

  h1 {
    font-size: 26px;
  }

  .textarea {
    min-height: 240px;
  }
}
</style>
