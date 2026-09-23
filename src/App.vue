<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'

const loaded = ref(false)
const menuOpen = ref(false)
const activeScene = ref(0)
let galleryTimer

const scenes = [
  { time: '06:12', title: 'The commute', note: 'Two buses. One coffee. Zero enthusiasm.', image: '/routine-commute.png' },
  { time: '14:47', title: 'The shift', note: 'Three screens, four deadlines, one jammed printer.', image: '/routine-office.png' },
  { time: '23:38', title: 'The “free time”', note: 'Laptop open. Brain closed. See you tomorrow.', image: '/routine-home.png' }
]
const scene = computed(() => scenes[activeScene.value])

const selectScene = index => {
  activeScene.value = index
  clearInterval(galleryTimer)
  galleryTimer = setInterval(() => activeScene.value = (activeScene.value + 1) % scenes.length, 4500)
}
onMounted(() => {
  setTimeout(() => loaded.value = true, 3600)
  galleryTimer = setInterval(() => activeScene.value = (activeScene.value + 1) % scenes.length, 4500)
})
onUnmounted(() => clearInterval(galleryTimer))
</script>

<template>
  <Transition name="loader"><div v-if="!loaded" class="loader-screen"><div class="office-loader"><div class="building-name">BURNOUT INC.</div><div class="office-windows"><i></i><i></i><i></i><i></i><i></i><i></i></div><div class="office-door"><span>9–5</span></div><div class="walk-cycle" aria-label="BurnoutHorse walking into the office"><img src="/burnout-walk.png" alt="" /><img src="/burnout-walk-2.png" alt="" /><img src="/burnout-walk-3.png" alt="" /></div><div class="street-line"></div></div><div class="loader-copy"><span>07:59:57</span><p>HERE WE GO AGAIN.</p></div></div></Transition>
  <main>
    <header>
      <a class="brand" href="#top"><span>BH</span><strong>BURNOUT<br />HORSE</strong></a>
      <nav :class="{ open: menuOpen }" @click="menuOpen = false"><a href="#story">Story</a><a href="#routine">Daily grind</a><a href="#review">Performance review</a></nav>
      <button class="menu" aria-label="Open menu" @click="menuOpen = !menuOpen">MENU</button>
    </header>
    <section id="top" class="hero">
      <img class="hero-bg" src="/hero-hoof.png" alt="BurnoutHorse leaving the financial district after work" /><div class="hero-overlay"></div><div class="hero-copy">
        <p class="overline">A VERY TIRED HORSE ON THE INTERNET</p><h1>BURNOUT<br /><em>HORSE</em></h1><p class="hero-line">He had dreams once.<br />Now he has calendar invites.</p>
        <div class="hero-bottom"><a class="main-cta" href="#routine">WATCH HIS DAY <span>↓</span></a><div class="socials" aria-label="Social links"><a href="#" aria-label="X"><svg viewBox="0 0 24 24"><path d="M5 4l14 16M19 4L5 20" /></svg></a><a href="#" aria-label="Telegram"><svg viewBox="0 0 24 24"><path d="M3 11l17-7-4 16-5-5-3 3 1-5z" /></svg></a><a href="#" aria-label="Dexscreener"><svg viewBox="0 0 24 24"><path d="M4 18l5-6 4 3 7-9M4 5v14h16" /></svg></a></div></div>
      </div>
      <div class="hero-stamp">MON–FRI<br /><strong>BARELY</strong></div>
      <div class="hero-ticker"><div><span>WORK • NAP • REPEAT •</span><span>WORK • NAP • REPEAT •</span><span>WORK • NAP • REPEAT •</span></div></div>
    </section>
    <section id="story" class="story"><div class="story-tag">MEET THE EMPLOYEE</div><div class="story-title"><span>01</span><h2>Just a horse.<br />With a <em>job.</em></h2></div><div class="story-copy"><p>BurnoutHorse used to run free. Then someone offered dental insurance and a suspiciously “competitive” salary.</p><p>Now he spends his days answering emails that could have been a nap. He is not a hero. He is us.</p></div><div class="quote">“Can we circle back<br />after my breakdown?”</div></section>
    <section id="routine" class="routine">
      <div class="routine-heading"><div><span>02 / LIVE FROM THE GRIND</span><h2>A day in<br />the <em>life.</em></h2></div><p>Every day is different.<br />Unfortunately, they all feel the same.</p></div>
      <div class="tv-wrap"><div class="tv"><div class="tv-top"><span>BH-TV</span><div>REC <i></i></div></div><div class="screen"><Transition name="scene" mode="out-in"><img :key="scene.image" :src="scene.image" :alt="scene.title" /></Transition><div class="scanlines"></div><span class="screen-time">{{ scene.time }}</span></div><div class="tv-caption"><strong>{{ scene.title }}</strong><p>{{ scene.note }}</p></div></div>
        <div class="channel-list"><button v-for="(item, index) in scenes" :key="item.time" :class="{ active: activeScene === index }" @click="selectScene(index)"><span>CH.0{{ index + 1 }}</span><strong>{{ item.time }}</strong><em>{{ item.title }}</em></button></div></div>
    </section>
    <section class="moodboard"><img src="/routine-commute.png" alt="BurnoutHorse on the morning bus" /><div><span>THE MORNING</span><strong>“Maybe traffic<br />will save me.”</strong></div><img src="/routine-home.png" alt="BurnoutHorse exhausted at home" /></section>
    <section id="review" class="review"><div class="review-copy"><span>03 / ANNUAL PERFORMANCE REVIEW</span><h2>MEETS<br /><em>EXPECTATIONS*</em></h2><p>*Expectations were lowered after Q2.</p></div><div class="review-grid"><article><span>TEAMWORK</span><strong>6/10</strong><p>Nods during meetings.</p></article><article><span>INITIATIVE</span><strong>3/10</strong><p>Opened the spreadsheet.</p></article><article><span>RESILIENCE</span><strong>∞</strong><p>Returned on Monday.</p></article><article><span>OVERALL</span><strong>TIRED</strong><p>Promote immediately.</p></article></div></section>
    <footer><div class="footer-horse">BURNOUT<br /><i>HORSE</i></div><div class="footer-small"><span>© 2026 — CLOCKED OUT, FINALLY.</span><div><a href="#">X</a><a href="#">TELEGRAM</a><a href="#">DEX</a></div></div><p>$HOOF is an entertainment token with no intrinsic value or expectation of financial return. Do your own research.</p></footer>
  </main>
</template>
