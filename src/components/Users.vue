<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

type userId = 1 | 2 | 3 | null;

interface IAlbums {
  userId: number,
  id: number,
  title: string,
}

const url = 'https://jsonplaceholder.typicode.com/albums';

let albums = ref<IAlbums[]>([]);
const isError = ref<boolean>(false);
const isLoading = ref<boolean>(true);
const idFilter = ref<userId>(1);

const sortedAlbums = computed(() => {
  if (albums.value.length) {
    return albums.value
      .filter((item) => item.userId === idFilter.value)
      .slice()
      .sort((a, b) => a.userId - b.userId);
  }
})

async function getAlbums(url: string) {
  try {
    const response = await fetch(url);
    if (!response.ok) throw Error(response.statusText)
    const data = await response.json();
    albums.value = data;
    isLoading.value = false;
  } catch (error) {
    console.error(error);
    isError.value = true;
    isLoading.value = false;
  }
}

onMounted(async () => {
  await getAlbums(url);
})
</script>

<template>
  <div class="main">
    <div v-if="!!albums.length" class="data">
      <div v-for="album in sortedAlbums" :key="album.id">
        {{ album.title }}
      </div>
    </div>
    <h2 v-else-if="isError">Something went wrong!</h2>
    <h2 v-else-if="!isLoading && !albums.length">There is no data!</h2>
    <div v-else-if="isLoading" class="loader"></div>
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
