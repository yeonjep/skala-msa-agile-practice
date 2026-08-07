<template>
  <router-link :to="`/courses/${course.id}`" class="course-card">
    <!-- 썸네일 -->
    <div class="card-thumb" :class="thumbBg">
      <div class="food-icon" aria-hidden="true">{{ foodIcon }}</div>
    </div>

    <!-- 내용 -->
    <div class="card-body">
      <div class="card-badges">
        <span class="badge" :class="badgeClass">{{ displayCategory }}</span>
        <span v-if="isNew" class="new-badge">신규</span>
      </div>
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
  '과일': { bg: 'thumb-teal', badge: 'badge-teal', icon: '🍎' },
  '채소': { bg: 'thumb-blue', badge: 'badge-blue', icon: '🥬' },
  '원두': { bg: 'thumb-amber', badge: 'badge-amber', icon: '☕️' },
  '간편식': { bg: 'thumb-pink', badge: 'badge-pink', icon: '🍲' },
  '베이커리': { bg: 'thumb-amber', badge: 'badge-amber', icon: '🥐' },
  '건강식': { bg: 'thumb-purple', badge: 'badge-purple', icon: '🥬' },
  '유제품': { bg: 'thumb-blue', badge: 'badge-blue', icon: '🥛' },
  '기타': { bg: 'thumb-gray', badge: 'badge-gray', icon: '🛒' },
}

const categoryLabelMap = {
  BACKEND: '과일',
  '백엔드': '과일',
  FRONTEND: '채소',
  '프론트엔드': '채소',
  DEVOPS: '원두',
  '데브옵스': '원두',
  DATA: '건강식',
  '데이터': '건강식',
  DATA_SCIENCE: '건강식',
  '데이터 사이언스': '건강식',
  MOBILE: '간편식',
  SECURITY: '베이커리',
  DATABASE: '유제품',
  OTHER: '기타',
}

const displayCategory = computed(() =>
  categoryLabelMap[props.course.category] || props.course.category || '기타'
)
const config = computed(() => categoryConfig[displayCategory.value] || { bg: 'thumb-gray', badge: 'badge-gray' })
const thumbBg = computed(() => config.value.bg)
const badgeClass = computed(() => config.value.badge)
const foodIcon = computed(() => config.value.icon || '🍽️')

const isNew = computed(() => {
  if (!props.course.createdAt) return false

  const createdAt = new Date(props.course.createdAt)
  if (Number.isNaN(createdAt.getTime())) return false

  const fourteenDays = 14 * 24 * 60 * 60 * 1000
  return Date.now() - createdAt.getTime() <= fourteenDays
})
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
.card-badges {
  display: flex;
  align-items: center;
  gap: 6px;
}
.new-badge {
  display: inline-flex;
  align-items: center;
  padding: 3px 7px;
  border-radius: 999px;
  background: #fff1f2;
  color: #e11d48;
  font-size: 10px;
  font-weight: 700;
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
