<script setup lang="ts">
import { ref, onBeforeUnmount } from 'vue';

const x = ref(window.innerWidth - 680);
const y = ref(window.innerHeight - 352);
const BUTTON_WIDTH = 140;
const BUTTON_HEIGHT = 56;

const moveRandom = () => {
  const maxX = window.innerWidth - BUTTON_WIDTH;
  const maxY = window.innerHeight - BUTTON_HEIGHT;
  x.value = Math.random() * maxX + BUTTON_WIDTH / 2;
  y.value = Math.random() * maxY + BUTTON_HEIGHT / 2;
};

const YES = ref<boolean>(false);
let heartInterval: ReturnType<typeof setInterval> | null = null;

const createHeart = () => {
  const heart = document.createElement('div');
  heart.className = 'heart-fly';
  heart.textContent = ['❤️', '💖', '💗', '💓', '💕', '♥️'][Math.floor(Math.random() * 6)];
  const size = 20 + Math.random() * 40;
  heart.style.fontSize = size + 'px';
  heart.style.left = Math.random() * window.innerWidth + 'px';
  heart.style.top = Math.random() * window.innerHeight + 'px';
  heart.style.animationDelay = Math.random() * 0.5 + 's';
  document.body.appendChild(heart);
  setTimeout(() => heart.remove(), 2000);
};

const loveAudio = new Audio('/love.mp3');
loveAudio.loop = true;

const handleYES = () => {
  YES.value = true;

  if (!heartInterval) {
    heartInterval = setInterval(() => {
      const count = 3 + Math.floor(Math.random() * 3);
      for (let i = 0; i < count; i++) createHeart();
    }, 300);
  }

  if (loveAudio.paused) {
    loveAudio.play().catch(err => {
      console.warn('Автовоспроизведение заблокировано браузером:', err);
    });
  }
};

onBeforeUnmount(() => {
  if (heartInterval) clearInterval(heartInterval);
  loveAudio.pause();
  loveAudio.currentTime = 0;
});

window.addEventListener('resize', moveRandom);
</script>

<template>
  <span class="absolute top-1/3 left-[25%] text-6xl font-mono font-bold z-2">Прости меня бусинка моя</span>
  <div class="relative pattern-bg flex items-center justify-center">
    <button
      class="border-2 mr-32 rounded-md cursor-pointer px-8 py-2 text-4xl font-semibold font-mono bg-pink-600 focus:animate-ping"
      @click="handleYES">
      ДА
    </button>
    <button
      class="absolute border-2 rounded-md cursor-pointer px-8 py-2 text-4xl font-semibold font-mono bg-pink-600 z-20"
      :style="{
        left: x + 'px',
        top: y + 'px',
        transform: 'translate(-50%, -50%)',
      }" @click="moveRandom" @mouseenter="moveRandom">
      НЕТ
    </button>
    <div v-if="YES"
      class="absolute flex items-center justify-center h-screen w-screen text-4xl z-40 bg-pink-600 font-mono">
      Я у тебя дурачок, но этот дурачок безумно тебя любит
    </div>
  </div>
</template>