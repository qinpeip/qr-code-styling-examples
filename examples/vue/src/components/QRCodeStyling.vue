<template>
  <div class="qr-demo">
    <h1>QR code styling for Vue</h1>
    <div class="layout">
      <aside class="controls">
        <section class="panel">
          <h2>基础</h2>
          <label class="field">
            <span>渲染类型</span>
            <select v-model="options.type">
              <option v-for="item in drawTypeOptions" :key="item.value" :value="item.value">{{ item.label }}</option>
            </select>
          </label>
          <label class="field">
            <span>宽度</span>
            <input v-model.number="options.width" type="number" min="50" max="2000" step="10">
          </label>
          <label class="field">
            <span>高度</span>
            <input v-model.number="options.height" type="number" min="50" max="2000" step="10">
          </label>
          <label class="field grow">
            <span>内容 (data)</span>
            <textarea v-model="options.data" :rows="3" placeholder="二维码数据"></textarea>
          </label>
        </section>

        <section class="panel">
          <h2>数据点 (dotsOptions)</h2>
          <label class="field">
            <span>点样式</span>
            <select v-model="options.dotsOptions.type">
              <option v-for="item in dotTypeOptions" :key="item.value" :value="item.value">{{ item.label }}</option>
            </select>
          </label>
          <label class="field">
            <span>颜色</span>
            <input v-model="options.dotsOptions.color" type="color">
          </label>
        </section>

        <section class="panel">
          <h2>背景 (backgroundOptions)</h2>
          <label class="field">
            <span>颜色</span>
            <input v-model="options.backgroundOptions.color" type="color">
          </label>
        </section>

        <section class="panel">
          <h2>定位角 — 外框 (cornersSquareOptions)</h2>
          <label class="field">
            <span>样式</span>
            <select v-model="options.cornersSquareOptions.type">
              <option v-for="item in cornerSquareTypeOptions" :key="item.value" :value="item.value">{{ item.label }}</option>
            </select>
          </label>
          <label class="field">
            <span>颜色</span>
            <input v-model="options.cornersSquareOptions.color" type="color">
          </label>
        </section>

        <section class="panel">
          <h2>定位角 — 内点 (cornersDotOptions)</h2>
          <label class="field">
            <span>样式</span>
            <select v-model="options.cornersDotOptions.type">
              <option v-for="item in cornerDotTypeOptions" :key="item.value" :value="item.value">{{ item.label }}</option>
            </select>
          </label>
          <label class="field">
            <span>颜色</span>
            <input v-model="options.cornersDotOptions.color" type="color">
          </label>
        </section>
      </aside>

      <div class="preview">
        <div id="qr-code" ref="qrCode" class="qr-wrap"></div>
        <div class="download-row">
          <label>
            文件名（可选）
            <input v-model="downloadName" type="text" placeholder="默认由浏览器命名">
          </label>
          <label>
            下载格式
            <select v-model="extension">
              <option v-for="item in extensionOptions" :key="item.value" :value="item.value">{{ item.label }}</option>
            </select>
          </label>
          <button type="button" @click="download">下载</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Options as ComponentOptions } from 'vue-class-component';
import QRCodeStyling, {
  DrawType,
  DotType,
  CornerSquareType,
  CornerDotType,
  Extension
} from 'qr-code-styling';
import type { Options as QROptions } from 'qr-code-styling';

const DRAW_TYPE_OPTIONS: { value: DrawType; label: string }[] = [
  { value: 'svg', label: 'SVG 矢量图' },
  { value: 'canvas', label: 'Canvas 画布' }
];

const DOT_TYPE_OPTIONS: { value: DotType; label: string }[] = [
  { value: 'dots', label: '圆点' },
  { value: 'rounded', label: '圆角矩形' },
  { value: 'classy', label: '精致圆角' },
  { value: 'classy-rounded', label: '精致大圆角' },
  { value: 'square', label: '方形' },
  { value: 'extra-rounded', label: '超圆角' }
];

const CORNER_SQUARE_TYPE_OPTIONS: { value: CornerSquareType; label: string }[] = [
  { value: 'dot', label: '圆点' },
  { value: 'square', label: '方形' },
  { value: 'extra-rounded', label: '圆角外框' }
];

const CORNER_DOT_TYPE_OPTIONS: { value: CornerDotType; label: string }[] = [
  { value: 'dot', label: '圆点' },
  { value: 'square', label: '方形' }
];

const EXTENSION_OPTIONS: { value: Extension; label: string }[] = [
  { value: 'svg', label: '矢量图（SVG）' },
  { value: 'png', label: '位图（PNG）' },
  { value: 'jpeg', label: '照片（JPEG）' },
  { value: 'webp', label: '网页图（WebP）' }
];

function buildDefaultOptions(): QROptions {
  return {
    width: 300,
    height: 300,
    type: 'svg',
    data: 'http://qr-code-styling.com',
    margin: 10,
    qrOptions: {
      typeNumber: 0,
      mode: 'Byte',
      errorCorrectionLevel: 'Q'
    },
    dotsOptions: {
      color: '#41b583',
      type: 'rounded'
    },
    backgroundOptions: {
      color: '#ffffff'
    },
    cornersSquareOptions: {
      color: '#35495E',
      type: 'extra-rounded'
    },
    cornersDotOptions: {
      color: '#35495E',
      type: 'dot'
    }
  };
}

@ComponentOptions({
  mounted(this: QRCodeStylingComponent) {
    const el = this.$refs['qrCode'] as HTMLElement;
    this.qrCode.append(el);
  },
  watch: {
    options: {
      handler(this: QRCodeStylingComponent) {
        this.qrCode.update(this.options);
      },
      deep: true
    }
  },
  methods: {
    download(this: QRCodeStylingComponent) {
      const name = this.downloadName.trim();
      this.qrCode.download({
        extension: this.extension as Extension,
        ...(name ? { name } : {})
      });
    }
  },
  data() {
    const options = buildDefaultOptions();
    return {
      options,
      extension: 'svg',
      downloadName: '',
      qrCode: new QRCodeStyling(options),
      drawTypeOptions: DRAW_TYPE_OPTIONS,
      dotTypeOptions: DOT_TYPE_OPTIONS,
      cornerSquareTypeOptions: CORNER_SQUARE_TYPE_OPTIONS,
      cornerDotTypeOptions: CORNER_DOT_TYPE_OPTIONS,
      extensionOptions: EXTENSION_OPTIONS
    };
  }
})
export default class QRCodeStylingComponent extends Vue {
  declare options: QROptions;
  declare extension: string;
  declare downloadName: string;
  declare qrCode: QRCodeStyling;
  declare drawTypeOptions: { value: DrawType; label: string }[];
  declare dotTypeOptions: { value: DotType; label: string }[];
  declare cornerSquareTypeOptions: { value: CornerSquareType; label: string }[];
  declare cornerDotTypeOptions: { value: CornerDotType; label: string }[];
  declare extensionOptions: { value: Extension; label: string }[];

  declare download: () => void;
}
</script>

<style scoped>
.qr-demo {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 16px 32px;
}

h1 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
}

.layout {
  display: flex;
  gap: 24px;
  align-items: flex-start;
}

.controls {
  flex: 0 0 min(420px, 100%);
  max-height: calc(100vh - 120px);
  overflow-y: auto;
  padding-right: 8px;
}

.preview {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
  position: sticky;
  top: 16px;
}

.qr-wrap {
  min-height: 320px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.panel {
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 12px 14px;
  margin-bottom: 12px;
  background: #fafafa;
}

.panel h2 {
  margin: 0 0 10px;
  font-size: 0.95rem;
  font-weight: 600;
  color: #333;
}

.field {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
  font-size: 0.875rem;
}

.field.grow {
  flex-wrap: wrap;
}

.field.grow span {
  flex: 0 0 100%;
}

.field span:first-child {
  flex: 0 0 140px;
  color: #555;
}

.field input[type="text"],
.field input[type="number"],
.field select,
.field textarea {
  flex: 1;
  min-width: 0;
  padding: 4px 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.download-row {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
}

@media (max-width: 900px) {
  .layout {
    flex-direction: column;
  }

  .controls {
    max-height: none;
  }

  .preview {
    position: static;
  }
}
</style>
