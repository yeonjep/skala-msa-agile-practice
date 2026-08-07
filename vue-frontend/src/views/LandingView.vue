<template>
  <div class="landing">
    <AppHeader />

    <!-- 히어로 섹션 -->
    <section class="hero">
      <div class="hero-inner">
        <div class="hero-content fade-in-up">
          <span class="hero-badge">맞춤 식품 구독 서비스</span>
          <h1 class="hero-title">익숙한 구독은 유지하고,<br>새로운 식품도 발견하세요</h1>
          <p class="hero-desc">구독 이력과 신규 상품 정보를 바탕으로 과일, 채소, 원두를 새롭게 추천합니다.</p>
          <div class="hero-actions">
            <router-link to="/login" class="btn btn-primary btn-lg">구독 시작하기</router-link>
            <router-link to="/courses" class="btn btn-outline btn-lg">상품 둘러보기</router-link>
          </div>
          <div class="hero-stats">
            <div class="stat"><span class="stat-num">1,200+</span><span class="stat-label">구독 상품</span></div>
            <div class="stat"><span class="stat-num">340+</span><span class="stat-label">공급사</span></div>
            <div class="stat"><span class="stat-num">28,000+</span><span class="stat-label">구독자</span></div>
          </div>
        </div>
        <div class="hero-visual fade-in">
          <div class="hero-food" aria-hidden="true">🥗</div>
        </div>
      </div>
    </section>

    <!-- 인기 상품 -->
    <section class="popular-section">
      <div class="section-inner">
        <div class="section-header">
          <h2 class="section-title">인기 상품</h2>
          <router-link to="/login" class="section-link">전체 보기 →</router-link>
        </div>
        <div class="course-grid">
          <div v-for="course in featuredCourses" :key="course.id" class="course-card-landing">
            <div class="card-thumb" :class="course.thumbBg">
              <span class="landing-food-icon" aria-hidden="true">{{ course.icon }}</span>
            </div>
            <div class="card-body">
              <span class="badge" :class="course.badgeClass">{{ course.category }}</span>
              <h3 class="card-title">{{ course.title }}</h3>
              <div class="card-meta">
              <span class="instructor">공급사 {{ course.instructor }}</span>
                <span class="price">{{ course.price }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 특징 섹션 -->
    <section class="features-section">
      <div class="section-inner">
        <h2 class="section-title center">왜 한끼픽인가요?</h2>
        <div class="features-grid">
          <div v-for="f in features" :key="f.title" class="feature-card">
            <div class="feature-icon">{{ f.icon }}</div>
            <h3 class="feature-title">{{ f.title }}</h3>
            <p class="feature-desc">{{ f.desc }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- CTA -->
    <section class="cta-section">
      <div class="cta-inner">
        <h2>오늘의 식단을 골라보세요</h2>
        <p>수천 명의 구독자가 한끼픽과 함께 건강한 식탁을 만들고 있습니다.</p>
        <router-link to="/login" class="btn btn-primary btn-lg">구독 시작하기</router-link>
      </div>
    </section>

    <!-- 푸터 -->
    <footer class="footer">
      <div class="footer-inner">
        <div class="footer-logo">
          <img src="@/assets/images/logo/main_logo.png" alt="한끼픽" />
          <span>한끼픽</span>
        </div>
        <p class="footer-copy">© 2026 한끼픽. All rights reserved.</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import AppHeader from '@/components/AppHeader.vue'

const featuredCourses = [
  { id:1, title:'제철 과일 정기 구독', category:'과일', instructor:'햇살과수원', price:'₩39,000', icon:'🍎', thumbBg:'thumb-teal', badgeClass:'badge-teal' },
  { id:2, title:'유기농 채소 꾸러미', category:'채소', instructor:'초록농장', price:'₩35,000', icon:'🥬', thumbBg:'thumb-blue', badgeClass:'badge-blue' },
  { id:3, title:'싱글 오리진 원두 구독', category:'원두', instructor:'로스터리온', price:'₩29,000', icon:'☕️', thumbBg:'thumb-amber', badgeClass:'badge-amber' },
  { id:4, title:'저당 건강식 도시락', category:'건강식', instructor:'웰니스랩', price:'₩49,000', icon:'🥗', thumbBg:'thumb-purple', badgeClass:'badge-purple' },
  { id:5, title:'간편한 국·찌개 세트', category:'간편식', instructor:'집밥연구소', price:'₩32,000', icon:'🍲', thumbBg:'thumb-pink', badgeClass:'badge-pink' },
  { id:6, title:'동네 빵집 베이커리 박스', category:'베이커리', instructor:'오븐하우스', price:'₩25,000', icon:'🥐', thumbBg:'thumb-amber', badgeClass:'badge-amber' },
]

const features = [
  { icon:'🥬', title:'다양한 식품 탐색', desc:'과일, 채소, 원두 등 새로운 카테고리의 상품을 만나볼 수 있습니다.' },
  { icon:'🎯', title:'AI 탐색 추천', desc:'선호 상품과 미탐색 카테고리를 함께 제안해 구독 매너리즘을 줄입니다.' },
  { icon:'💳', title:'간편한 구독 신청', desc:'원클릭 결제로 원하는 식품 구독을 시작할 수 있습니다.' },
  { icon:'📦', title:'정기 배송 안내', desc:'매주 필요한 식품을 편리하게 받아볼 수 있습니다.' },
]
</script>

<style scoped>
.landing { background: var(--color-bg-secondary); }

/* 히어로 */
.hero {
  background: linear-gradient(135deg, #f4fbf4 0%, #e7f5e9 50%, #f8fcf4 100%);
  border-bottom: 1px solid var(--color-border);
  padding: 80px 0 64px;
}
.hero-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 24px;
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 48px;
  align-items: center;
}
.hero-badge {
  display: inline-block;
  padding: 5px 14px;
  background: var(--color-primary-light);
  color: var(--color-primary);
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
  margin-bottom: 16px;
}
.hero-title {
  font-size: 42px;
  font-weight: 700;
  line-height: 1.25;
  letter-spacing: -0.5px;
  color: var(--color-text-primary);
  margin-bottom: 16px;
}
.hero-desc {
  font-size: 16px;
  color: var(--color-text-secondary);
  line-height: 1.7;
  max-width: 460px;
  margin-bottom: 28px;
}
.hero-actions {
  display: flex;
  gap: 12px;
  margin-bottom: 40px;
}
.btn-lg { padding: 12px 28px; font-size: 15px; }
.hero-stats {
  display: flex;
  gap: 36px;
}
.stat { display: flex; flex-direction: column; gap: 2px; }
.stat-num { font-size: 22px; font-weight: 700; color: var(--color-primary); }
.stat-label { font-size: 12px; color: var(--color-text-secondary); }
.hero-visual {
  display: flex;
  align-items: center;
  justify-content: center;
}
.hero-food { font-size: 150px; line-height: 1; filter: drop-shadow(0 14px 14px rgba(47, 125, 50, 0.2)); }

/* 강의 섹션 */
.popular-section { padding: 64px 0; }
.section-inner { max-width: 1200px; margin: 0 auto; padding: 0 24px; }
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}
.section-title { font-size: 22px; font-weight: 700; color: var(--color-text-primary); }
.section-title.center { text-align: center; margin-bottom: 40px; }
.section-link { font-size: 14px; color: var(--color-primary); font-weight: 500; }
.section-link:hover { text-decoration: underline; }

.course-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}
.course-card-landing {
  background: var(--color-bg-primary);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  overflow: hidden;
  transition: var(--transition);
}
.course-card-landing:hover {
  transform: translateY(-3px);
  box-shadow: var(--shadow-md);
}
.card-thumb {
  height: 110px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.thumb-teal   { background: #E1F5EE; }
.thumb-blue   { background: #E6F1FB; }
.thumb-purple { background: #EEEDFE; }
.thumb-pink   { background: #FBEAF0; }
.landing-food-icon { font-size: 48px; line-height: 1; }
.card-body { padding: 14px 16px; display: flex; flex-direction: column; gap: 6px; }
.card-title { font-size: 14px; font-weight: 600; color: var(--color-text-primary); line-height: 1.4; }
.card-meta { display: flex; justify-content: space-between; align-items: center; }
.instructor { font-size: 12px; color: var(--color-text-secondary); }
.price { font-size: 14px; font-weight: 600; color: var(--color-primary); }

/* 특징 */
.features-section { padding: 64px 0; background: var(--color-bg-primary); }
.features-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
}
.feature-card {
  padding: 28px 24px;
  background: var(--color-bg-secondary);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  text-align: center;
  transition: var(--transition);
}
.feature-card:hover { box-shadow: var(--shadow-md); transform: translateY(-2px); }
.feature-icon { font-size: 32px; margin-bottom: 12px; }
.feature-title { font-size: 15px; font-weight: 600; margin-bottom: 8px; }
.feature-desc { font-size: 13px; color: var(--color-text-secondary); line-height: 1.6; }

/* CTA */
.cta-section {
  padding: 80px 0;
  background: linear-gradient(135deg, var(--color-primary) 0%, var(--color-primary-dark) 100%);
  text-align: center;
}
.cta-inner { max-width: 600px; margin: 0 auto; padding: 0 24px; }
.cta-inner h2 { font-size: 32px; font-weight: 700; color: #fff; margin-bottom: 12px; }
.cta-inner p { font-size: 16px; color: rgba(255,255,255,0.8); margin-bottom: 32px; }
.cta-inner .btn-primary {
  background: #fff;
  color: var(--color-primary);
  border-color: #fff;
  font-weight: 600;
}
.cta-inner .btn-primary:hover { background: #f0f7ff; }

/* 푸터 */
.footer {
  background: var(--color-text-primary);
  padding: 32px 0;
}
.footer-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.footer-logo {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #fff;
  font-size: 15px;
  font-weight: 600;
}
.footer-logo img { width: 28px; height: 28px; border-radius: 6px; }
.footer-copy { font-size: 13px; color: rgba(255,255,255,0.5); }
</style>
