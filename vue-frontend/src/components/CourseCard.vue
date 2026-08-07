<template>
  <router-link :to="`/courses/${course.id}`" class="course-card">
    <!-- 썸네일 -->
    <div class="card-thumb" :class="thumbBg">
      <div class="food-icon" aria-hidden="true">{{ foodIcon }}</div>
    </div>

    <!-- 내용 -->
    <div class="card-body">
      <span class="badge" :class="badgeClass">{{ displayCategory }}</span>
      <h3 class="card-title">{{ course.title }}</h3>
      <div class="card-meta">
        <span class="instructor">공급사 {{ course.instructorName || '-' }}</span>
        <span class="price">₩{{ Number(course.price).toLocaleString() }}</span>
      </div>
      <div class="card-footer">
        <span class="enrolled">구독자 {{ course.enrollmentCount?.toLocaleString() || 0 }}명</span>
      </div>
    </div>
  </router-link>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  course: { type: Object, required: true }
})

const categoryConfig = {
  '밀키트': { bg: 'thumb-teal', badge: 'badge-teal', icon: '🍱' },
  '샐러드': { bg: 'thumb-blue', badge: 'badge-blue', icon: '🥗' },
  '베이커리': { bg: 'thumb-amber', badge: 'badge-amber', icon: '🥐' },
  '건강식': { bg: 'thumb-purple', badge: 'badge-purple', icon: '🥬' },
  '간편식': { bg: 'thumb-pink', badge: 'badge-pink', icon: '🍲' },
}

const categoryLabelMap = {
  BACKEND: '밀키트',
  FRONTEND: '샐러드',
  DEVOPS: '베이커리',
  DATA: '건강식',
  DATA_SCIENCE: '건강식',
  AI: '간편식',
}

const displayCategory = computed(() =>
  categoryLabelMap[props.course.category] || props.course.category || '기타'
)
const config = computed(() => categoryConfig[displayCategory.value] || { bg: 'thumb-gray', badge: 'badge-gray' })
const thumbBg = computed(() => config.value.bg)
const badgeClass = computed(() => config.value.badge)
const foodIcon = computed(() => config.value.icon || '🍽️')
</script>

<style scoped>
.course-card {
  display: flex;
  flex-direction: column;
  background: var(--color-bg-primary);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  overflow: hidden;
  transition: var(--transition);
  cursor: pointer;
}
.course-card:hover {
  transform: translateY(-3px);
  box-shadow: var(--shadow-md);
  border-color: var(--color-border-hover);
}
.card-thumb {
  height: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.thumb-teal   { background: #E1F5EE; }
.thumb-blue   { background: #E6F1FB; }
.thumb-amber  { background: #FAEEDA; }
.thumb-purple { background: #EEEDFE; }
.thumb-pink   { background: #FBEAF0; }
.thumb-gray   { background: #F1EFE8; }
.food-icon { font-size: 48px; line-height: 1; }
.card-body {
  padding: 14px 16px;
  display: flex;
  flex-direction: column;
  gap: 6px;
  flex: 1;
}
.card-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--color-text-primary);
  line-height: 1.4;
}
.card-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.instructor {
  font-size: 12px;
  color: var(--color-text-secondary);
}
.price {
  font-size: 14px;
  font-weight: 600;
  color: var(--color-primary);
}
.card-footer {
  margin-top: 2px;
}
.enrolled {
  font-size: 11px;
  color: var(--color-text-muted);
}
</style>
