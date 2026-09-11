<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'
import { Award, Download, ExternalLink, Search, X, ChevronLeft, ChevronRight, Trophy, Sparkles } from '@lucide/vue'
import { honors } from './data/honors'

const level = ref('全部奖项')
const query = ref('')
const activeHonor = ref(null)

const levels = ['全部奖项', '一等奖', '二等奖', '三等奖', '优秀奖', '其他荣誉']
const base = import.meta.env.BASE_URL
const assetUrl = (path) => `${base}honors/${path}`
const isPdf = (path) => path.toLowerCase().endsWith('.pdf')
const coverUrl = (honor) => assetUrl(honor.cover || honor.file)

const filteredHonors = computed(() => {
  const needle = query.value.trim().toLowerCase()
  return honors.filter((honor) => {
    const levelMatch = level.value === '全部奖项'
      || (level.value === '其他荣誉' ? !['一等奖', '二等奖', '三等奖', '优秀奖'].includes(honor.level) : honor.level === level.value)
    const queryMatch = !needle || `${honor.title}${honor.subtitle}${honor.category}${honor.year}`.toLowerCase().includes(needle)
    return levelMatch && queryMatch
  })
})

const featured = computed(() => honors.filter((item) => ['一等奖', '二等奖', '三等奖'].includes(item.level)).length)
const activeIndex = computed(() => filteredHonors.value.findIndex((item) => item.id === activeHonor.value?.id))

function openHonor(honor) {
  activeHonor.value = honor
  document.body.classList.add('modal-open')
}

function closeHonor() {
  activeHonor.value = null
  document.body.classList.remove('modal-open')
}

function move(direction) {
  if (!filteredHonors.value.length) return
  const next = (activeIndex.value + direction + filteredHonors.value.length) % filteredHonors.value.length
  activeHonor.value = filteredHonors.value[next]
}

function onKeydown(event) {
  if (!activeHonor.value) return
  if (event.key === 'Escape') closeHonor()
  if (event.key === 'ArrowLeft') move(-1)
  if (event.key === 'ArrowRight') move(1)
}

onMounted(() => window.addEventListener('keydown', onKeydown))
onUnmounted(() => window.removeEventListener('keydown', onKeydown))
</script>

<template>
  <div class="site-shell">
    <header class="topbar">
      <a class="brand" href="#top" aria-label="返回顶部">
        <span class="brand-mark"><Award :size="19" /></span>
        <span>黄钶卿 · 荣誉档案</span>
      </a>
      <nav aria-label="页面导航">
        <a href="#honors">荣誉墙</a>
        <a href="#journey">成长轨迹</a>
      </nav>
    </header>

    <main id="top">
      <section class="hero" aria-labelledby="page-title">
        <div class="hero-copy">
          <p class="eyebrow">HONORS & MILESTONES · 2022—2025</p>
          <h1 id="page-title">荣誉不是终点，<br><em>是成长留下的刻度。</em></h1>
          <p class="intro">从校园实践到全国赛事，记录每一次认真投入、每一段持续精进。</p>
          <a class="primary-action" href="#honors">浏览全部荣誉 <ChevronRight :size="18" /></a>
        </div>
        <div class="hero-gallery" aria-hidden="true">
          <figure class="hero-card hero-card-main"><img :src="coverUrl(honors[0])" alt="" /></figure>
          <figure class="hero-card hero-card-top"><img :src="coverUrl(honors[4])" alt="" /></figure>
          <figure class="hero-card hero-card-bottom"><img :src="coverUrl(honors[10])" alt="" /></figure>
          <span class="gallery-caption"><Sparkles :size="14" /> SELECTED WORKS</span>
        </div>
        <div class="hero-stats" aria-label="荣誉统计">
          <div><strong>{{ honors.length }}</strong><span>份荣誉记录</span></div>
          <div><strong>{{ featured }}</strong><span>项等级奖项</span></div>
          <div><strong>04</strong><span>年成长跨度</span></div>
        </div>
        <div class="hero-seal" aria-hidden="true"><Trophy :size="46" stroke-width="1.35" /><span>HONOR</span></div>
      </section>

      <section id="honors" class="honors-section">
        <div class="section-heading">
          <div>
            <p class="section-index">01 / HONOR ARCHIVE</p>
            <h2>荣誉墙</h2>
          </div>
          <p>每一张证书背后，都是一段真实的行动。</p>
        </div>

        <div class="filters" aria-label="荣誉筛选">
          <p class="filter-label">ARCHIVE INDEX <span>按关键词快速定位荣誉</span></p>
          <div class="filter-right">
            <label class="search-box">
              <Search :size="17" />
              <input v-model="query" type="search" placeholder="搜索赛事或领域" aria-label="搜索荣誉" />
            </label>
            <select v-model="level" aria-label="按奖项等级筛选">
              <option v-for="item in levels" :key="item">{{ item }}</option>
            </select>
          </div>
        </div>

        <div class="result-meta"><span>{{ filteredHonors.length }} 项记录</span><span class="rule"></span></div>

        <div v-if="filteredHonors.length" class="honor-grid">
          <article v-for="(honor, index) in filteredHonors" :key="honor.id" class="honor-card" tabindex="0" @click="openHonor(honor)" @keydown.enter="openHonor(honor)">
            <div class="card-image">
              <img :src="coverUrl(honor)" :alt="`${honor.title}证书`" loading="lazy" />
              <span class="card-number">{{ String(index + 1).padStart(2, '0') }}</span>
              <button class="view-button" title="查看证书" aria-label="查看证书"><ExternalLink :size="18" /></button>
            </div>
            <div class="card-body">
              <div class="card-meta"><span>{{ honor.year }}</span><span>{{ honor.category }}</span></div>
              <h3>{{ honor.title }}</h3>
              <p>{{ honor.subtitle }}</p>
              <span class="level" :data-level="honor.level">{{ honor.level }}</span>
            </div>
          </article>
        </div>
        <div v-else class="empty-state">
          <Search :size="28" />
          <h3>没有找到相关荣誉</h3>
          <button @click="query = ''; level = '全部奖项'">清除筛选</button>
        </div>
      </section>

      <section id="journey" class="journey-section">
        <div class="section-heading light">
          <div><p class="section-index">02 / THE JOURNEY</p><h2>成长轨迹</h2></div>
          <p>阶段会变化，向前的方向始终清晰。</p>
        </div>
        <div class="timeline">
          <div class="timeline-item"><span class="year">2022</span><strong>从校园出发</strong><p>以财经素养竞赛为起点，建立持续参赛与复盘的习惯。</p></div>
          <div class="timeline-item"><span class="year">2023</span><strong>积累与担当</strong><p>奖学金、学生工作与校园实践，让成长不止于专业能力。</p></div>
          <div class="timeline-item"><span class="year">2024</span><strong>跨向技术赛场</strong><p>网页开发、云计算与视觉设计，多方向探索能力边界。</p></div>
          <div class="timeline-item current"><span class="year">2025</span><strong>走向更大的舞台</strong><p>在人工智能、创新创业与数字技术赛事中持续突破。</p></div>
        </div>
      </section>
    </main>

    <footer><span>黄钶卿 · 个人荣誉档案</span><span>KEEP LEARNING · KEEP BUILDING</span></footer>

    <Transition name="modal">
      <div v-if="activeHonor" class="modal" role="dialog" aria-modal="true" :aria-label="activeHonor.title" @click.self="closeHonor">
        <button class="modal-close" title="关闭" aria-label="关闭" @click="closeHonor"><X :size="22" /></button>
        <button class="modal-nav prev" title="上一项" aria-label="上一项" @click="move(-1)"><ChevronLeft :size="25" /></button>
        <div class="modal-content">
          <div class="modal-visual">
            <iframe v-if="isPdf(activeHonor.file)" :src="assetUrl(activeHonor.file)" :title="activeHonor.title"></iframe>
            <img v-else :src="assetUrl(activeHonor.file)" :alt="`${activeHonor.title}证书原图`" />
          </div>
          <aside class="modal-info">
            <p class="section-index">{{ activeHonor.year }} · HONOR ARCHIVE</p>
            <h2>{{ activeHonor.title }}</h2>
            <p>{{ activeHonor.subtitle }}</p>
            <div class="modal-tags"><span>{{ activeHonor.level }}</span><span>{{ activeHonor.category }}</span></div>
            <a :href="assetUrl(activeHonor.file)" target="_blank" download><Download :size="18" /> 下载 / 查看原件</a>
          </aside>
        </div>
        <button class="modal-nav next" title="下一项" aria-label="下一项" @click="move(1)"><ChevronRight :size="25" /></button>
      </div>
    </Transition>
  </div>
</template>
