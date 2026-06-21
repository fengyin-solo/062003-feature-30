<template>
  <header class="game-header">
    <div class="header-top">
      <div class="header-left">
        <button class="btn ghost" @click="$emit('back')">← 存档</button>
        <h2>第 {{ state.day }} 天</h2>
        <span class="days-left">剩余 {{ daysLeft }} 天</span>
      </div>
      <div class="header-stats">
        <div class="stat-item">
          <span class="label">资金</span>
          <span class="value" :class="{ danger: state.money < 10000 }">
            ¥{{ state.money.toLocaleString() }}
          </span>
        </div>
        <div class="stat-item">
          <span class="label">粉丝</span>
          <span class="value">{{ state.fans.toLocaleString() }}</span>
        </div>
        <div class="stat-item">
          <span class="label">盈利</span>
          <span class="value" :class="profit >= 0 ? 'success' : 'danger'">
            ¥{{ profit.toLocaleString() }}
          </span>
        </div>
        <div class="stat-item">
          <span class="label">出道</span>
          <span class="value">{{ state.groups.length }}/{{ targetGroups }}</span>
        </div>
      </div>
      <button class="theme-btn" @click="$emit('toggle-theme')">
        {{ theme === 'light' ? '🌙' : '☀️' }}
      </button>
    </div>

    <div class="milestone-bar" v-if="milestoneProgress">
      <div class="milestone-info">
        <span class="milestone-label">
          🏆 阶段目标：{{ milestoneProgress.currentMilestone?.label || '—' }}
          <span class="milestone-desc">{{ milestoneProgress.currentMilestone?.desc }}</span>
        </span>
        <span class="milestone-count">
          已达成 {{ milestoneProgress.completedCount }}/{{ milestoneProgress.totalMilestones }}
        </span>
      </div>
      <div class="progress-track">
        <div
          class="progress-fill"
          :style="{ width: (milestoneProgress.overallProgress * 100) + '%' }"
        ></div>
        <div
          v-for="(m, idx) in milestoneProgress.milestones"
          :key="idx"
          class="milestone-marker"
          :class="{
            completed: m.completed,
            reached: m.reached && !m.completed,
            current: m.current,
          }"
          :style="{ left: (m.day / milestoneProgress.totalDays * 100) + '%' }"
          :title="`${m.label}：${m.desc}${m.completed ? ' ✓ 已达成' : m.reached ? ' (未达标)' : ''}`"
        >
          <span class="marker-dot"></span>
          <span class="marker-label">{{ m.label }}</span>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
import { GAME_CONFIG } from '../config/gameConfig'

defineProps({
  state: Object,
  daysLeft: Number,
  profit: Number,
  theme: String,
  milestoneProgress: Object,
})
defineEmits(['back', 'toggle-theme'])

const targetGroups = GAME_CONFIG.victory.targetGroups
</script>

<style scoped>
.game-header {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  padding: 1rem 1.25rem;
  background: var(--bg-card);
  border-bottom: 1px solid var(--border);
}

.header-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  flex-wrap: wrap;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.header-left h2 {
  font-size: 1.25rem;
}

.days-left {
  font-size: 0.85rem;
  color: var(--text-muted);
}

.header-stats {
  display: flex;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.stat-item .label {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.stat-item .value {
  font-weight: 700;
  font-size: 1rem;
}

.stat-item .value.success { color: var(--success); }
.stat-item .value.danger { color: var(--danger); }

.theme-btn {
  background: var(--bg-secondary);
  border: 1px solid var(--border);
  border-radius: 50%;
  width: 40px;
  height: 40px;
  cursor: pointer;
  font-size: 1.1rem;
}

.milestone-bar {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding-top: 0.25rem;
  border-top: 1px solid var(--border);
}

.milestone-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.85rem;
}

.milestone-label {
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.milestone-desc {
  color: var(--text-muted);
  font-weight: 400;
  font-size: 0.8rem;
}

.milestone-count {
  color: var(--text-muted);
}

.progress-track {
  position: relative;
  height: 8px;
  background: var(--bg-secondary);
  border-radius: 4px;
  overflow: visible;
}

.progress-fill {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background: linear-gradient(90deg, var(--accent), var(--success));
  border-radius: 4px;
  transition: width 0.3s ease;
}

.milestone-marker {
  position: absolute;
  top: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: help;
}

.marker-dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: var(--bg-secondary);
  border: 2px solid var(--text-muted);
  transition: all 0.2s ease;
}

.milestone-marker.completed .marker-dot {
  background: var(--success);
  border-color: var(--success);
}

.milestone-marker.reached .marker-dot {
  background: var(--warning, #f59e0b);
  border-color: var(--warning, #f59e0b);
}

.milestone-marker.current .marker-dot {
  background: var(--accent);
  border-color: var(--accent);
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.3);
  transform: scale(1.2);
}

.marker-label {
  position: absolute;
  top: 18px;
  font-size: 0.7rem;
  color: var(--text-muted);
  white-space: nowrap;
}

.milestone-marker.completed .marker-label {
  color: var(--success);
}

.milestone-marker.current .marker-label {
  color: var(--accent);
  font-weight: 600;
}
</style>
