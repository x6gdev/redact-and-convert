<script setup lang="ts">
const props = defineProps<{
  modelValue: string
  privacyEnabled: boolean
}>()

const emit = defineEmits<{
  (event: 'update:modelValue', value: string): void
  (event: 'update:privacyEnabled', value: boolean): void
  (event: 'upload', payload: { name: string; text: string }): void
  (event: 'clear'): void
  (event: 'format'): void
  (event: 'run'): void
}>()

function onTextInput(event: Event): void {
  const target = event.target as HTMLTextAreaElement
  emit('update:modelValue', target.value)
}

function onUpload(event: Event): void {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (!file) return

  const reader = new FileReader()
  reader.onload = () => {
    const text = typeof reader.result === 'string' ? reader.result : ''
    emit('upload', { name: file.name, text })
  }
  reader.readAsText(file)
  target.value = ''
}
</script>

<template>
  <section class="panel">
    <header class="panel-header">
      <div class="header-main">
        <h2>Input</h2>
        <span class="file-name" v-if="props.modelValue && props.modelValue.length > 0">
          {{ props.modelValue.length }} characters
        </span>
      </div>
      
      <div class="panel-actions">
        <!-- Main Convert Action -->
        <button type="button" class="primary-btn sm" @click="emit('run')" :disabled="!props.modelValue">
          Convert
        </button>

        <!-- Dedicated Format Utility -->
        <button type="button" class="ghost-btn sm" title="Prettify / Format input" @click="emit('format')" :disabled="!props.modelValue">
          ✨ <span class="hide-mobile">Format</span>
        </button>

        <!-- Privacy Toggle -->
        <button
          type="button"
          class="ghost-btn sm"
          :class="{ active: props.privacyEnabled }"
          @click="emit('update:privacyEnabled', !props.privacyEnabled)"
        >
          🛡️ <span class="hide-mobile">Privacy</span>
        </button>

        <!-- Custom Upload -->
        <label class="upload-btn-v2">
          <input type="file" accept=".json,.csv,.yaml,.yml,.txt" @change="onUpload" />
          <span class="ghost-btn sm">📁 <span class="hide-mobile">Upload</span></span>
        </label>

        <!-- Clear -->
        <button type="button" class="ghost-btn sm" @click="emit('clear')" :disabled="!props.modelValue">
          🧹
        </button>
      </div>
    </header>

    <textarea
      class="text-area"
      placeholder="Paste content here or upload a file..."
      aria-label="Input content"
      :value="props.modelValue"
      @input="onTextInput"
    />
  </section>
</template>
