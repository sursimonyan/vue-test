<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

type userId = 1 | 2 | 3 | null;

interface IAlbums {
  userId: number,
  id: number,
  title: string,
}

const url = 'https://jsonplaceholder.typicode.com/albums';

const albums = ref<IAlbums[]>([]);
const isError = ref<boolean>(false);
const idFilter = ref<userId>(1);

const sortedAlbums = computed(() => {
  if (albums.value.length) {
    const newAlbums = albums.value.filter((item) => item.userId === idFilter.value);
    return newAlbums.sort((a, b) => a.userId > b.userId);
  }
})

async function getAlbums(url) {
  try {
    const response = await fetch(url);
    if (!response.ok) throw Error(response.statusText)
    const data = await response.json();
    albums.value = data;
  } catch (error) {
    console.error(error.message);
    isError.value = true;
  }
}

onMounted(async () => {
  await getAlbums(url);
})
</script>

<template>
  <div class="main">
    {{ isError }}
    <div v-if="!!albums.length" class="data">
      <div v-for="album in sortedAlbums" :key="album.id">
        {{ album.title }}
      </div>
    </div>
    <h2 v-else-if="isError">Something weny wrong or no data!</h2>
    <div v-else-if="!albums.length && !isError" class="loader"></div>
  </div>
</template>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 1rem;
}

.data {
  display: flex;
  flex-direction: column;
  row-gap: 1rem;
}

h2 {
  margin: auto;
  font-size: 3rem;
  font-weight: 700;
}

/* Loader */
.loader {
  margin: auto;
  width: 48px;
  height: 48px;
  border: 5px solid #000000;
  border-bottom-color: transparent;
  border-radius: 50%;
  animation: rotation 1s linear infinite;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
