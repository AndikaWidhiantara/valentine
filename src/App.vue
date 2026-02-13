<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

import herImg from './asset/her.png'
import meImg from './asset/me.png'
import loveImg from './asset/love.png'
import couple1Img from './asset/couple1.png'
import couple2Img from './asset/couple2.png'

const photos = ref([
  {
    id: 1,
    src: herImg,
    label: 'Her 💗',
    song: 'Indahnya Dirimu',
    artist: 'HIVI!',
    youtubeId: 'IGqTw72o5wI',
  },
  {
    id: 2,
    src: meImg,
    label: 'Me 💙',
    song: 'Remaja',
    artist: 'HIVI!',
    youtubeId: 'AxlZlM-M75E',
  },
  {
    id: 3,
    src: loveImg,
    label: 'Our Love 💜',
    song: 'Siapkah Kau Tuk Jatuh Cinta Lagi',
    artist: 'HIVI!',
    youtubeId: 'kX1O93X77d4',
  },
  {
    id: 4,
    src: couple1Img,
    label: 'Together 🌈',
    song: 'Pelangi',
    artist: 'HIVI!',
    youtubeId: 'bBjENsfyvFs',
  },
  {
    id: 5,
    src: couple2Img,
    label: 'Forever 💕',
    song: 'Tersenyum, Untuk Siapa?',
    artist: 'HIVI!',
    youtubeId: 'iSyxz2RVddA',
  },
])

const activePhoto = ref(null)
const isPlaying = ref(false)
const hearts = ref([])

function playMusic(photo) {
  if (activePhoto.value && activePhoto.value.id === photo.id) {
    activePhoto.value = null
    isPlaying.value = false
    return
  }
  activePhoto.value = photo
  isPlaying.value = true
}

function getYoutubeEmbedUrl(id) {
  return `https://www.youtube.com/embed/${id}?autoplay=1&loop=1&playlist=${id}`
}

function createHeart() {
  const heart = {
    id: Date.now() + Math.random(),
    left: Math.random() * 100,
    size: Math.random() * 20 + 10,
    duration: Math.random() * 5 + 5,
    delay: Math.random() * 3,
    opacity: Math.random() * 0.5 + 0.2,
  }
  hearts.value.push(heart)
  setTimeout(() => {
    hearts.value = hearts.value.filter(h => h.id !== heart.id)
  }, (heart.duration + heart.delay) * 1000)
}

let heartInterval = null
onMounted(() => {
  for (let i = 0; i < 15; i++) {
    createHeart()
  }
  heartInterval = setInterval(createHeart, 800)
})

onUnmounted(() => {
  if (heartInterval) clearInterval(heartInterval)
})
</script>

<template>
  <div class="valentine-app">
    <!-- Floating hearts background -->
    <div class="hearts-container">
      <span
        v-for="heart in hearts"
        :key="heart.id"
        class="floating-heart"
        :style="{
          left: heart.left + '%',
          fontSize: heart.size + 'px',
          animationDuration: heart.duration + 's',
          animationDelay: heart.delay + 's',
          opacity: heart.opacity,
        }"
      >♥</span>
    </div>

    <!-- Ambient glow orbs -->
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
    <div class="orb orb-3"></div>

    <!-- Header -->
    <header class="header">
      <div class="header-decoration">✦</div>
      <h1 class="title">
        <span class="title-line">Happy Valentine's Day</span>
        <span class="title-heart">💕</span>
      </h1>
      <p class="subtitle">Setiap foto menyimpan melodi cinta kita</p>
      <div class="header-line"></div>
    </header>

    <!-- Running Text -->
    <div class="marquee-wrapper">
      <div class="marquee-track">
        <span class="marquee-text">💕 Happy Valentine Cayang, Makasih Ya Udah Hadir Dihidup Aku, Maaf Belum Bisa Menjadi Seperti Yang Kamu Inginkan 💕&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
        <span class="marquee-text">💕 Happy Valentine Cayang, Makasih Ya Udah Hadir Dihidup Aku, Maaf Belum Bisa Menjadi Seperti Yang Kamu Inginkan 💕&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span>
      </div>
    </div>

    <!-- Photo Gallery -->
    <main class="gallery">
      <div
        v-for="photo in photos"
        :key="photo.id"
        class="photo-card"
        :class="{ active: activePhoto && activePhoto.id === photo.id }"
        @click="playMusic(photo)"
      >
        <div class="photo-glow"></div>
        <div class="photo-wrapper">
          <img :src="photo.src" :alt="photo.label" class="photo-img" />
          <div class="photo-overlay">
            <div class="play-icon">
              <svg v-if="!(activePhoto && activePhoto.id === photo.id)" viewBox="0 0 24 24" fill="currentColor" width="40" height="40">
                <path d="M8 5v14l11-7z"/>
              </svg>
              <svg v-else viewBox="0 0 24 24" fill="currentColor" width="40" height="40">
                <path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z"/>
              </svg>
            </div>
            <span class="song-name">{{ photo.song }}</span>
            <span class="artist-name">{{ photo.artist }}</span>
          </div>
        </div>
        <div class="photo-label">{{ photo.label }}</div>

        <!-- Equalizer animation when playing -->
        <div v-if="activePhoto && activePhoto.id === photo.id" class="equalizer">
          <span></span><span></span><span></span><span></span><span></span>
        </div>
      </div>
    </main>

    <!-- Now Playing Bar -->
    <Transition name="slide-up">
      <div v-if="activePhoto" class="now-playing">
        <div class="now-playing-content">
          <div class="now-playing-pulse"></div>
          <div class="now-playing-info">
            <span class="now-playing-label">♫ Sedang Diputar</span>
            <span class="now-playing-song">{{ activePhoto.song }} — {{ activePhoto.artist }}</span>
          </div>
          <button class="stop-btn" @click.stop="activePhoto = null; isPlaying = false" title="Berhenti">
            ✕
          </button>
          <!-- Tiny YouTube iframe for audio only -->
          <div class="audio-iframe">
            <iframe
              :key="activePhoto.youtubeId"
              :src="getYoutubeEmbedUrl(activePhoto.youtubeId)"
              allow="autoplay; encrypted-media"
              frameborder="0"
            ></iframe>
          </div>
        </div>
      </div>
    </Transition>

    <!-- Footer -->
    <footer class="footer">
      <p>Made with ♥ for you</p>
    </footer>
  </div>
</template>

<style>
/* ====== GLOBAL RESET & BASE ====== */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  overflow-x: hidden;
  max-width: 100%;
}

body {
  font-family: 'Poppins', sans-serif;
  background: #0a0008;
  color: #fff;
  min-height: 100vh;
}

/* ====== VALENTINE APP ====== */
.valentine-app {
  min-height: 100vh;
  background: linear-gradient(
    135deg,
    #0a0008 0%,
    #1a0011 20%,
    #2d0a1f 40%,
    #1a0520 60%,
    #0d0018 80%,
    #0a0008 100%
  );
  position: relative;
  overflow-x: clip;
  overflow-y: visible;
}

/* ====== MARQUEE RUNNING TEXT ====== */
.marquee-wrapper {
  position: relative;
  z-index: 2;
  width: auto;
  max-width: 100vw;
  overflow: hidden;
  padding: 14px 0;
  margin: 0 30px 10px;
  background: linear-gradient(90deg, transparent, rgba(255, 107, 157, 0.08), transparent);
  border-top: 1px solid rgba(255, 107, 157, 0.12);
  border-bottom: 1px solid rgba(255, 107, 157, 0.12);
  border-radius: 12px;
}

.marquee-track {
  display: inline-flex;
  white-space: nowrap;
  animation: marqueeScroll 18s linear infinite;
}

.marquee-text {
  font-family: 'Playfair Display', serif;
  font-size: clamp(0.9rem, 2.2vw, 1.15rem);
  font-weight: 600;
  font-style: italic;
  background: linear-gradient(90deg, #ff6b9d, #ff9ec4, #c44dff, #ff9ec4, #ff6b9d);
  background-size: 300% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: shimmer 5s ease-in-out infinite;
  white-space: nowrap;
  letter-spacing: 1px;
}

@keyframes marqueeScroll {
  0% { transform: translateX(0); }
  100% { transform: translateX(-50%); }
}

@keyframes shimmer {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

/* ====== AMBIENT ORB EFFECTS ====== */
.orb {
  position: fixed;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
  z-index: 0;
}

.orb-1 {
  width: 400px;
  height: 400px;
  background: radial-gradient(circle, rgba(255, 50, 100, 0.15), transparent);
  top: -100px;
  right: -100px;
  animation: orbFloat 12s ease-in-out infinite;
}

.orb-2 {
  width: 300px;
  height: 300px;
  background: radial-gradient(circle, rgba(200, 50, 200, 0.12), transparent);
  bottom: 10%;
  left: -80px;
  animation: orbFloat 15s ease-in-out infinite reverse;
}

.orb-3 {
  width: 250px;
  height: 250px;
  background: radial-gradient(circle, rgba(255, 100, 150, 0.1), transparent);
  top: 50%;
  right: 20%;
  animation: orbFloat 10s ease-in-out infinite 2s;
}

@keyframes orbFloat {
  0%, 100% { transform: translate(0, 0) scale(1); }
  25% { transform: translate(30px, -20px) scale(1.1); }
  50% { transform: translate(-20px, 30px) scale(0.95); }
  75% { transform: translate(15px, 15px) scale(1.05); }
}

/* ====== FLOATING HEARTS ====== */
.hearts-container {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 1;
  overflow: hidden;
}

.floating-heart {
  position: absolute;
  bottom: -50px;
  color: rgba(255, 80, 130, 0.6);
  animation: floatUp linear forwards;
  text-shadow: 0 0 10px rgba(255, 80, 130, 0.3);
}

@keyframes floatUp {
  0% {
    transform: translateY(0) rotate(0deg) scale(1);
    opacity: 0;
  }
  10% {
    opacity: var(--heart-opacity, 0.4);
  }
  90% {
    opacity: var(--heart-opacity, 0.4);
  }
  100% {
    transform: translateY(-110vh) rotate(360deg) scale(0.5);
    opacity: 0;
  }
}

/* ====== HEADER ====== */
.header {
  position: relative;
  z-index: 2;
  text-align: center;
  padding: 60px 20px 40px;
}

.header-decoration {
  font-size: 18px;
  color: rgba(255, 150, 180, 0.6);
  letter-spacing: 20px;
  margin-bottom: 16px;
  animation: sparkle 3s ease-in-out infinite;
}

@keyframes sparkle {
  0%, 100% { opacity: 0.5; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.1); }
}

.title {
  font-family: 'Playfair Display', serif;
  font-weight: 700;
  margin-bottom: 16px;
}

.title-line {
  display: block;
  font-size: clamp(2rem, 6vw, 3.5rem);
  background: linear-gradient(135deg, #ff6b9d, #ff9ec4, #ffb8d4, #ff6b9d);
  background-size: 300% 300%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: gradientShift 6s ease-in-out infinite;
  text-shadow: none;
}

.title-heart {
  display: inline-block;
  font-size: clamp(2.5rem, 7vw, 4rem);
  animation: heartbeat 1.5s ease-in-out infinite;
  filter: drop-shadow(0 0 20px rgba(255, 100, 150, 0.5));
}

@keyframes gradientShift {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

@keyframes heartbeat {
  0%, 100% { transform: scale(1); }
  15% { transform: scale(1.15); }
  30% { transform: scale(1); }
  45% { transform: scale(1.1); }
}

.subtitle {
  font-size: clamp(0.9rem, 2.5vw, 1.15rem);
  color: rgba(255, 180, 200, 0.7);
  font-weight: 300;
  font-style: italic;
  letter-spacing: 2px;
}

.header-line {
  width: 80px;
  height: 2px;
  background: linear-gradient(90deg, transparent, #ff6b9d, transparent);
  margin: 24px auto 0;
  border-radius: 1px;
}

/* ====== PHOTO GALLERY ====== */
.gallery {
  position: relative;
  z-index: 2;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 18px;
  max-width: 1100px;
  margin: 0 auto;
  padding: 20px 24px 60px;
}

/* ====== PHOTO CARD ====== */
.photo-card {
  position: relative;
  cursor: pointer;
  border-radius: 20px;
  overflow: visible;
  transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1), box-shadow 0.4s ease;
}

.photo-card:hover {
  transform: translateY(-8px) scale(1.02);
}

.photo-card.active {
  transform: translateY(-5px) scale(1.02);
}

.photo-glow {
  position: absolute;
  inset: -2px;
  border-radius: 22px;
  background: linear-gradient(135deg, #ff6b9d, #c44dff, #ff6b9d);
  background-size: 200% 200%;
  opacity: 0;
  transition: opacity 0.4s ease;
  z-index: -1;
  animation: gradientShift 4s ease-in-out infinite;
}

.photo-card:hover .photo-glow,
.photo-card.active .photo-glow {
  opacity: 1;
}

.photo-card.active .photo-glow {
  box-shadow: 0 0 40px rgba(255, 107, 157, 0.4), 0 0 80px rgba(196, 77, 255, 0.2);
}

.photo-wrapper {
  position: relative;
  border-radius: 18px;
  overflow: hidden;
  aspect-ratio: 3/4;
  background: #1a0011;
}

.photo-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.6s ease, filter 0.4s ease;
}

.photo-card:hover .photo-img {
  transform: scale(1.08);
  filter: brightness(0.7);
}

.photo-card.active .photo-img {
  transform: scale(1.05);
  filter: brightness(0.6) saturate(1.2);
}

.photo-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(
    to top,
    rgba(10, 0, 8, 0.9) 0%,
    rgba(10, 0, 8, 0.4) 40%,
    rgba(10, 0, 8, 0.2) 100%
  );
  opacity: 0;
  transition: opacity 0.4s ease;
  padding: 20px;
}

.photo-card:hover .photo-overlay,
.photo-card.active .photo-overlay {
  opacity: 1;
}

.play-icon {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  background: rgba(255, 107, 157, 0.2);
  backdrop-filter: blur(10px);
  border: 2px solid rgba(255, 107, 157, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  margin-bottom: 16px;
  transition: all 0.3s ease;
  box-shadow: 0 0 30px rgba(255, 107, 157, 0.3);
}

.photo-card:hover .play-icon {
  background: rgba(255, 107, 157, 0.35);
  transform: scale(1.1);
  box-shadow: 0 0 40px rgba(255, 107, 157, 0.5);
}

.photo-card.active .play-icon {
  background: rgba(255, 107, 157, 0.5);
  border-color: #ff6b9d;
  animation: pulseIcon 2s ease-in-out infinite;
}

@keyframes pulseIcon {
  0%, 100% { box-shadow: 0 0 30px rgba(255, 107, 157, 0.4); }
  50% { box-shadow: 0 0 50px rgba(255, 107, 157, 0.7); }
}

.song-name {
  font-family: 'Playfair Display', serif;
  font-size: 1rem;
  font-weight: 600;
  text-align: center;
  color: #fff;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
  margin-bottom: 4px;
}

.artist-name {
  font-size: 0.8rem;
  color: rgba(255, 180, 210, 0.8);
  font-weight: 400;
}

.photo-label {
  text-align: center;
  padding: 14px 10px;
  font-family: 'Playfair Display', serif;
  font-size: 1.1rem;
  font-weight: 600;
  color: rgba(255, 200, 220, 0.9);
  letter-spacing: 1px;
  background: linear-gradient(to bottom, rgba(26, 0, 17, 0.95), rgba(10, 0, 8, 0.98));
  border-radius: 0 0 18px 18px;
  margin-top: -2px;
}

/* ====== EQUALIZER ====== */
.equalizer {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  gap: 3px;
  height: 20px;
  position: absolute;
  bottom: 50px;
  left: 50%;
  transform: translateX(-50%);
}

.equalizer span {
  width: 4px;
  background: linear-gradient(to top, #ff6b9d, #ff9ec4);
  border-radius: 2px;
  animation: equalize 0.8s ease-in-out infinite;
}

.equalizer span:nth-child(1) { animation-delay: 0s; height: 8px; }
.equalizer span:nth-child(2) { animation-delay: 0.15s; height: 14px; }
.equalizer span:nth-child(3) { animation-delay: 0.3s; height: 10px; }
.equalizer span:nth-child(4) { animation-delay: 0.1s; height: 16px; }
.equalizer span:nth-child(5) { animation-delay: 0.25s; height: 6px; }

@keyframes equalize {
  0%, 100% { height: 4px; }
  50% { height: 18px; }
}

/* ====== NOW PLAYING BAR ====== */
.now-playing {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100;
  background: linear-gradient(135deg, rgba(26, 0, 20, 0.95), rgba(45, 10, 31, 0.95));
  backdrop-filter: blur(20px);
  border-top: 1px solid rgba(255, 107, 157, 0.3);
  box-shadow: 0 -10px 40px rgba(0, 0, 0, 0.5), 0 -2px 20px rgba(255, 107, 157, 0.15);
}

.now-playing-content {
  max-width: 800px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 14px 24px;
}

.now-playing-pulse {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: #ff6b9d;
  animation: pulse 1.5s ease-in-out infinite;
  flex-shrink: 0;
}

@keyframes pulse {
  0%, 100% {
    box-shadow: 0 0 0 0 rgba(255, 107, 157, 0.6);
  }
  50% {
    box-shadow: 0 0 0 10px rgba(255, 107, 157, 0);
  }
}

.now-playing-info {
  display: flex;
  flex-direction: column;
  flex: 1;
  min-width: 0;
}

.now-playing-label {
  font-size: 0.7rem;
  color: rgba(255, 180, 200, 0.7);
  text-transform: uppercase;
  letter-spacing: 2px;
  font-weight: 500;
}

.now-playing-song {
  font-family: 'Playfair Display', serif;
  font-size: 1rem;
  font-weight: 600;
  color: #fff;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.stop-btn {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: 1px solid rgba(255, 107, 157, 0.4);
  background: rgba(255, 107, 157, 0.15);
  color: #ff9ec4;
  font-size: 16px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  flex-shrink: 0;
}

.stop-btn:hover {
  background: rgba(255, 107, 157, 0.35);
  border-color: #ff6b9d;
  transform: scale(1.1);
}

/* ====== AUDIO IFRAME (HIDDEN) ====== */
.audio-iframe {
  width: 1px;
  height: 1px;
  overflow: hidden;
  opacity: 0.01;
  position: absolute;
  bottom: 0;
  right: 0;
}

.audio-iframe iframe {
  width: 1px;
  height: 1px;
}

/* ====== TRANSITIONS ====== */
.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.slide-up-enter-from,
.slide-up-leave-to {
  transform: translateY(100%);
  opacity: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* ====== FOOTER ====== */
.footer {
  position: relative;
  z-index: 2;
  text-align: center;
  padding: 30px 20px 90px;
  color: rgba(255, 180, 200, 0.4);
  font-size: 0.85rem;
  font-style: italic;
  letter-spacing: 2px;
}

/* ====== RESPONSIVE ====== */
@media (max-width: 900px) {
  .gallery {
    grid-template-columns: repeat(3, 1fr);
    gap: 14px;
    padding: 10px 16px 50px;
  }

  .header {
    padding: 40px 16px 24px;
  }

  .play-icon {
    width: 45px;
    height: 45px;
  }

  .play-icon svg {
    width: 24px;
    height: 24px;
  }

  .song-name {
    font-size: 0.75rem;
  }

  .photo-label {
    font-size: 0.85rem;
    padding: 8px 6px;
  }
}

@media (max-width: 480px) {
  .gallery {
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    max-width: 100%;
  }

  .now-playing-song {
    font-size: 0.85rem;
  }
}
</style>
