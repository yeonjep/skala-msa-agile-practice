<template>
  <div class="page-wrapper">
    <AppHeader />
    <div class="page-layout">
      <aside class="sidebar">
        <div class="sidebar-section">
          <div class="sidebar-label">메뉴</div>

          <router-link to="/courses" class="sidebar-item">
            <span class="si-icon">🛒</span> 상품 목록
          </router-link>

          <router-link
            v-if="!isInstructor"
            to="/enrollments"
            class="sidebar-item active"
          >
            <span class="si-icon">✅</span> 내 구독 목록
          </router-link>

          <router-link to="/mypage" class="sidebar-item">
            <span class="si-icon">⭐</span> 마이페이지
          </router-link>
        </div>

        <div class="sidebar-section">
          <div class="sidebar-label">계정</div>
          <router-link to="/mypage" class="sidebar-item">
            <span class="si-icon">👤</span> 마이페이지
          </router-link>
          <button class="sidebar-item sidebar-btn" @click="handleLogout">
            <span class="si-icon">🚪</span> 로그아웃
          </button>
        </div>
      </aside>

      <main class="main-content">
        <h1 class="page-title">내 구독 목록</h1>

        <div v-if="loading" class="loading-center">
          <div class="spinner"></div>
        </div>

        <div v-else-if="enrollments.length" class="enrollment-list fade-in">
          <div v-for="item in enrollments" :key="item.id" class="enrollment-card">
            <div class="enroll-thumb" :class="getThumbBg(item.course?.category)">
              <span class="enroll-food-icon" aria-hidden="true">{{ getFoodIcon(item.course?.category) }}</span>
            </div>

            <div class="enroll-info">
              <span class="badge" :class="getBadge(item.course?.category)">
                {{ displayCategory(item.course?.category) }}
              </span>
              <h3 class="enroll-title">{{ item.course?.title }}</h3>
              <p class="enroll-instructor">공급사: {{ item.course?.instructorName || '정보 없음' }}</p>
            </div>

            <div class="enroll-status">
              <span
                :class="[
                  'status-badge',
                  getStatusClass(item.status)
                ]"
              >
                {{ getStatusLabel(item.status) }}
              </span>
              <router-link :to="`/courses/${item.courseId}`" class="btn btn-ghost btn-sm">
                상품 보기
              </router-link>
              <button
                v-if="item.status !== 'CANCELLED'"
                type="button"
                class="btn btn-cancel btn-sm"
                @click="cancelSubscription(item)"
              >
                구독 취소
              </button>
            </div>
          </div>
        </div>

        <div v-else class="empty-state">
          <p class="empty-icon">📭</p>
          <p>구독 중인 상품이 없습니다.</p>
          <router-link to="/courses" class="btn btn-primary" style="margin-top:16px;">
            상품 둘러보기
          </router-link>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import AppHeader from '@/components/AppHeader.vue'
import { enrollmentApi } from '@/api/enrollment.js'
import { useAuthStore } from '@/store/auth.js'

const router = useRouter()
const auth = useAuthStore()

const enrollments = ref([])
const loading = ref(true)

const isInstructor = computed(() => auth.user?.role === 'INSTRUCTOR')

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

function displayCategory(category) {
  return categoryLabelMap[category] || category || '기타'
}

function getThumbBg(cat) {
  return categoryConfig[displayCategory(cat)]?.bg || 'thumb-gray'
}

function getBadge(cat) {
  return categoryConfig[displayCategory(cat)]?.badge || 'badge-gray'
}

function getFoodIcon(category) {
  return categoryConfig[displayCategory(category)]?.icon || '🍽️'
}

function getStatusClass(status) {
  if (status === 'ACTIVE') return 'status-active'
  if (status === 'CANCELLED') return 'status-cancelled'
  return 'status-pending'
}

function getStatusLabel(status) {
  if (status === 'ACTIVE') return '구독 중'
  if (status === 'CANCELLED') return '구독 취소됨'
  return '구독 접수 · 결제 처리 중'
}

function cancelSubscription(item) {
  const confirmed = window.confirm(`'${item.course?.title || '이 상품'}' 구독을 취소할까요?`)
  if (!confirmed) return

  // 발표용 프론트 상태 처리입니다. 새로고침하면 서버의 기존 상태를 다시 불러옵니다.
  item.status = 'CANCELLED'
}

function handleLogout() {
  auth.logout()
  router.push('/')
}

onMounted(async () => {
  // 강사는 이 페이지 접근 불가 → 마이페이지로 이동
  if (isInstructor.value) {
    console.warn('[EnrollmentView] instructor tried to access /enrollments, redirect to /mypage')
    router.replace('/mypage')
    return
  }

  try {
    const res = await enrollmentApi.getMyEnrollments()
    console.log('[EnrollmentView] my enrollments response:', res.data)

    if (Array.isArray(res.data?.data)) {
      enrollments.value = res.data.data
    } else if (Array.isArray(res.data)) {
      enrollments.value = res.data
    } else {
      enrollments.value = []
    }
  } catch (error) {
    console.error('[EnrollmentView] failed to load enrollments:', error)
    enrollments.value = []
  } finally {
    loading.value = false
  }
})
</script>

<style scoped>
.page-wrapper {
  min-height: 100vh;
  background: var(--color-bg-secondary);
}

.page-layout {
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px 24px;
  display: grid;
  grid-template-columns: 220px 1fr;
  gap: 28px;
}

.sidebar {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.sidebar-section {
  display: flex;
  flex-direction: column;
  gap: 2px;
  margin-bottom: 8px;
}

.sidebar-label {
  font-size: 10px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--color-text-muted);
  padding: 8px 12px 4px;
}

.sidebar-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 9px 12px;
  border-radius: var(--radius-md);
  font-size: 14px;
  color: var(--color-text-secondary);
  transition: var(--transition);
  background: none;
  border: none;
  width: 100%;
  text-align: left;
  cursor: pointer;
  font-family: var(--font-sans);
  text-decoration: none;
}

.sidebar-item:hover {
  background: var(--color-bg-tertiary);
  color: var(--color-text-primary);
}

.sidebar-item.active {
  background: var(--color-primary-light);
  color: var(--color-primary);
  font-weight: 500;
}

.si-icon {
  font-size: 15px;
}

.main-content {
  min-width: 0;
}

.page-title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 24px;
}

.enrollment-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.enrollment-card {
  display: flex;
  align-items: center;
  gap: 16px;
  background: var(--color-bg-primary);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: 16px;
  transition: var(--transition);
}

.enrollment-card:hover {
  box-shadow: var(--shadow-sm);
}

.enroll-thumb {
  width: 72px;
  height: 72px;
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  overflow: hidden;
}

.enroll-food-icon { font-size: 34px; line-height: 1; }

.thumb-teal {
  background: #E1F5EE;
}

.thumb-blue {
  background: #E6F1FB;
}

.thumb-amber {
  background: #FAEEDA;
}

.thumb-purple {
  background: #EEEDFE;
}

.thumb-pink {
  background: #FBEAF0;
}

.thumb-gray {
  background: #F1EFE8;
}

.enroll-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.enroll-title {
  font-size: 15px;
  font-weight: 600;
}

.enroll-instructor {
  font-size: 13px;
  color: var(--color-text-secondary);
}

.enroll-status {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 8px;
}

.status-badge {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 500;
}

.status-active {
  background: #E1F5EE;
  color: #0F6E56;
}

.status-pending {
  background: #FAEEDA;
  color: #854F0B;
}

.status-cancelled {
  background: #F1F1EF;
  color: #6B6B66;
}

.btn-sm {
  padding: 7px 14px;
  font-size: 13px;
}

.btn-cancel {
  border: 1px solid #fecaca;
  background: #fff;
  color: #dc2626;
}

.btn-cancel:hover {
  border-color: #fca5a5;
  background: #fef2f2;
}

.empty-state {
  text-align: center;
  padding: 80px 0;
  color: var(--color-text-muted);
}

.empty-icon {
  font-size: 48px;
  margin-bottom: 12px;
}

.loading-center {
  display: flex;
  justify-content: center;
  padding: 80px 0;
}

.spinner {
  width: 36px;
  height: 36px;
  border: 3px solid var(--color-border);
  border-top-color: var(--color-primary);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>
