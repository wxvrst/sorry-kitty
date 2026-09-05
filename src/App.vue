<script setup lang="ts">
import { ref } from 'vue';

const x = ref(56);
const y = ref(50);

const moveRandom = () => {
  const maxX = 100 - 10;
  const maxY = 100 - 10;
  x.value = 10 + Math.random() * (maxX - 10);
  y.value = 10 + Math.random() * (maxY - 10);
};

const YES = ref<boolean>(false);
const visible = ref<boolean>(true);
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

const loveAudio = new Audio('love.mp3');
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

window.addEventListener('resize', moveRandom);

const words = ['Киса', 'прости меня, пожалуйста', 'я действительно сожалею', 'я тебя очень сильно люблю', 'очень люблю', 'очень приочень люблю', 'безумно люблю', 'тебя люблю',]

function* generator() {
  for (const word of words) {
    yield word
  }
}

const myGenerator = generator();

var buttonText = ref<string>('Нажми меня');

const handleDear = () => {
  const next = myGenerator.next();
  if (!next.done) {
    buttonText.value = next.value;
  }
  if (next.done) {
    visible.value = false;
  }
}

</script>

<template>
  <div v-if="visible"
    class="absolute flex items-center justify-center h-screen w-screen text-4xl z-40 bg-rose-600 font-mono">
    <button
      class="border-2 rounded-md cursor-pointer px-8 py-2 text-4xl font-semibold font-mono bg-rose-500 transition-all duration-400 hover:scale-106 hover:bg-rose-400 drop-shadow-md drop-shadow-rose-200"
      @click="handleDear">
      {{ buttonText }}
    </button>
  </div>
  <div class="absolute top-1/3 text-4xl font-mono font-bold z-2 w-full lg:text-6xl text-center">Прости меня бусинка моя
  </div>
  <div v-if="!visible" class="relative pattern-bg flex items-center justify-center">
    <button
      class="border-2 lg:mr-32 mr-42 rounded-md cursor-pointer px-8 py-2 text-4xl font-semibold font-mono bg-pink-600 focus:animate-ping transition-all duration-400 hover:scale-106 hover:bg-pink-500 drop-shadow-md drop-shadow-pink-500"
      @click="handleYES">
      ДА
    </button>
    <button
      class="absolute ml-10 lg:ml-0 border-2 rounded-md cursor-pointer px-8 py-2 text-4xl font-semibold font-mono bg-pink-600 z-20 transition-all duration-100 hover:scale-106 hover:bg-pink-500 drop-shadow-md drop-shadow-pink-500"
      :style="{
        left: x + '%',
        top: y + '%',
        transform: 'translate(-50%, -50%)',
      }" @click="moveRandom" @mouseenter="moveRandom">
      НЕТ
    </button>
    <div v-if="YES"
      class="italic absolute flex items-center justify-center h-screen w-screen text-4xl z-40 bg-pink-600 font-mono">
      Я у тебя дурачок, но этот дурачок безумно тебя любит
    </div>
  </div>
</template>