<script setup>
import { ref, computed } from 'vue'
import gsap from 'gsap'

const list = [
  { msg: 'Брюс Лі' },
  { msg: 'Джекі Чан' },
  { msg: 'Чак Норріс' },
  { msg: 'Джет Лі' },
  { msg: 'Кунг Ф`юрі' }
]

const query = ref('')

const computedList = computed(() => {
  return list.filter((item) => item.msg.toLowerCase().includes(query.value))
})

function onBeforeEnter(el) {
  el.style.opacity = 0
  el.style.height = 0
}

function onEnter(el, done) {
  gsap.to(el, {
    opacity: 1,
    height: '1.6em',
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}

function onLeave(el, done) {
  gsap.to(el, {
    opacity: 0,
    height: 0,
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}
</script>

<template>
  <div class="demo" style="height: 265px">
    <input v-model="query" style="margin-bottom: 20px" />
    <TransitionGroup
      tag="ul"
      :css="false"
      @before-enter="onBeforeEnter"
      @enter="onEnter"
      @leave="onLeave"
    >
      <li
        v-for="(item, index) in computedList"
        :key="item.msg"
        :data-index="index"
      >
        {{ item.msg }}
      </li>
    </TransitionGroup>
  </div>
</template>
