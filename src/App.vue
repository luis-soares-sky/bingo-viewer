<script setup lang="ts">
  import { ref } from 'vue';

  const numberCurrent = ref(0);
  const numberPrevious = ref(0);
  const numberGrid = ref<number[]>([]);
  const numberPool = ref<number[]>(generateShuffledArray(90));

  function generateShuffledArray(length: number): number[] {
    const arr = Array.from({ length }, (_, i) => i + 1);
    shuffleArray(arr);
    return arr;
  }

  function shuffleArray(arr: number[]): void {
    let currentIndex = arr.length;
    while (currentIndex != 0) {
      let randomIndex = Math.floor(Math.random() * currentIndex);
      currentIndex--;
      [arr[currentIndex], arr[randomIndex]] = [arr[randomIndex], arr[currentIndex]];
    }
  }

  function hasBeenDrawn(num: number): boolean {
    return numberGrid.value.includes(num);
  }

  function drawNumber() {
    const num = numberPool.value.pop();
    if (!!num) {
      numberPrevious.value = numberCurrent.value;
      numberCurrent.value = num;
      numberGrid.value.push(num);
    }
  }

  function getButtonLabel() {
    if (numberCurrent.value == 0) return 'START';
    if (numberPool.value.length < 1) return 'FINISHED!';
    return 'NEXT';
  }
</script>

<template>
  <button @click="drawNumber">{{ getButtonLabel() }}</button>
  <header>
    <span class="number-previous">{{ numberPrevious == 0 ? '' : numberPrevious }}</span>
    <span class="number-current">{{ numberCurrent == 0 ? 'START' : numberCurrent }}</span>
    <span class="number-next">??</span>
  </header>
  <main>
    <div class="number-grid">
      <div :class="[
        'number-cell',
        hasBeenDrawn(x) ? '-drawn' : '',
        x == numberCurrent ? '-current' : '',
      ]" v-for="x in 90">
        <span>{{ x }}</span>
      </div>
    </div>
  </main>
</template>

<style scoped>
  header {
    display: flex;
    align-items: center;
    flex-direction: row;
    justify-content: space-evenly;
    width: 100%;
  }
  header > span {
    display: block;
    flex-basis: 33%;
    font-size: 4rem;
    font-weight: bold;
    text-align: center;
  }
  header > span.number-current {
    font-size: 6rem;
  }
  header > span.number-next {
    filter: blur(10px);
  }
  main {
    display: block;
  }
  .number-grid {
    display: grid;
    text-align: center;
    grid-template-columns: repeat(10, 2rem);
    gap: 1rem;
    justify-content: center;
  }
  .number-cell > span {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 2rem;
    height: 2rem;
    background: #222;
    color: #666;
    border-radius: 50%;
    font-weight: bold;
  }
  .number-cell.-drawn > span {
    background: #666;
    color: white;
  }
  .number-cell.-current > span {
    background: red;
    color: white;
  }
</style>
