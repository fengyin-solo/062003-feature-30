<template>
  <div class="risk-warning" v-if="hasWarnings">
    <div
      v-for="(w, idx) in warnings"
      :key="idx"
      class="warning-card"
      :class="w.level"
    >
      <div class="warning-header">
        <span class="warning-icon">{{ w.icon }}</span>
        <span class="warning-title">{{ w.title }}</span>
      </div>
      <p class="warning-message">{{ w.message }}</p>
    </div>

    <div v-if="nearFailure.isNear" class="near-failure-card">
      <div class="near-failure-header">
        <span class="near-failure-icon">⚠️</span>
        <span class="near-failure-title">临近失败预警</span>
      </div>
      <ul class="near-failure-reasons">
        <li v-for="(r, i) in nearFailure.reasons" :key="i">{{ r }}</li>
      </ul>
      <div v-if="nearFailure.suggestions.length > 0" class="near-failure-suggestions">
        <span class="suggestion-label">建议：</span>
        <ul>
          <li v-for="(s, i) in nearFailure.suggestions" :key="i">💡 {{ s }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  warnings: {
    type: Array,
    default: () => [],
  },
  nearFailure: {
    type: Object,
    default: () => ({ isNear: false, reasons: [], suggestions: [] }),
  },
})

const hasWarnings = computed(() => {
  return props.warnings.length > 0 || props.nearFailure.isNear
})
</script>

<style scoped>
.risk-warning {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.warning-card {
  padding: 0.75rem 1rem;
  border-radius: 8px;
  border: 1px solid var(--border);
  background: var(--bg-card);
}

.warning-card.warning {
  border-left: 4px solid #f59e0b;
  background: linear-gradient(90deg, rgba(245, 158, 11, 0.08) 0%, var(--bg-card) 30%);
}

.warning-card.critical {
  border-left: 4px solid var(--danger);
  background: linear-gradient(90deg, rgba(239, 68, 68, 0.1) 0%, var(--bg-card) 30%);
  animation: pulse-critical 2s ease-in-out infinite;
}

@keyframes pulse-critical {
  0%, 100% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.2); }
  50% { box-shadow: 0 0 0 4px rgba(239, 68, 68, 0.1); }
}

.warning-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.25rem;
}

.warning-icon {
  font-size: 1.1rem;
}

.warning-title {
  font-weight: 700;
  font-size: 0.9rem;
}

.warning-card.warning .warning-title {
  color: #f59e0b;
}

.warning-card.critical .warning-title {
  color: var(--danger);
}

.warning-message {
  font-size: 0.8rem;
  color: var(--text-muted);
  margin: 0;
  line-height: 1.4;
}

.near-failure-card {
  padding: 1rem;
  border-radius: 8px;
  border: 2px solid var(--danger);
  background: linear-gradient(135deg, rgba(239, 68, 68, 0.12) 0%, var(--bg-card) 60%);
  animation: shake 0.5s ease-in-out;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-2px); }
  75% { transform: translateX(2px); }
}

.near-failure-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.near-failure-icon {
  font-size: 1.25rem;
}

.near-failure-title {
  font-weight: 700;
  font-size: 1rem;
  color: var(--danger);
}

.near-failure-reasons {
  margin: 0.25rem 0 0.5rem 0;
  padding-left: 1.25rem;
  font-size: 0.85rem;
}

.near-failure-reasons li {
  color: var(--text-primary);
  margin-bottom: 0.15rem;
}

.near-failure-suggestions {
  padding-top: 0.5rem;
  border-top: 1px dashed var(--border);
}

.suggestion-label {
  font-size: 0.85rem;
  font-weight: 600;
  color: var(--accent);
}

.near-failure-suggestions ul {
  margin: 0.25rem 0 0 0;
  padding-left: 1.25rem;
  font-size: 0.8rem;
}

.near-failure-suggestions li {
  color: var(--text-muted);
  margin-bottom: 0.15rem;
}
</style>
