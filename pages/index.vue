<template>
  <NuxtLayout>
    <div id="IndexPage" class="w-full overflow-auto">
      <div class="mx-auto max-w-[500px] overflow-hidden">
        <div class="px-4 max-w-[600px] mx-auto" id="Posts">
          <div v-if="isPosts" v-for="post in posts" :key="post">
            <Post :post="post" @isDeleted="posts = userStore.getAllPosts()"></Post>
          </div>
          <div v-else>
            <client-only>
              <div
                v-if="isLoading"
                class="mt-20 w-full flex items-center justify-center mx-auto"
              >
                <div
                  class="text-white mx-auto flex flex-col items-center justify-center"
                >
                  <Icon
                    name="eos-icons:bubble-loading"
                    size="50"
                    color="#ffffff"
                  />
                  <div class="w-full mt-1">Loading...</div>
                </div>
              </div>
              <div
                v-if="!isLoading"
                class="mt-20 w-full flex items-center justify-center mx-auto"
              >
                <div
                  class="text-white mx-auto flex flex-col items-center justify-center"
                >
                  <Icon name="tabler:mood-empty" size="50" color="#ffffff" />
                  <div class="w-full">Make the first post!</div>
                </div>
              </div>
            </client-only>
          </div>
        </div>
      </div>
    </div>
  </NuxtLayout>
</template>

<script setup>
definePageMeta({
  layout: "main",
});

import { useUserStore } from "../stores/user";
const userStore = useUserStore();
const user = useSupabaseUser();
watchEffect(() => {
  if (!user.value) {
    return navigateTo("/auth");
  }
});
let posts = ref([]);
let isPosts = ref(false);
let isLoading = ref(false);

onBeforeMount(async () => {
  try {
    isLoading.value = true;
    await userStore.getAllPosts();
    isLoading.value = false;
  } catch (error) {
    console.log(error);
  }
});

onMounted(() => {
  watchEffect(() => {
    posts.value = userStore.posts
    if (userStore.posts && userStore.posts.length >= 1) {
      isPosts.value = true;
    } else{
        isPosts.value = false
    }
  });
});
watch(
  () => posts.value,
  () => {
    posts.value = userStore.posts
    if (userStore.posts && userStore.posts.length >= 1) {
      isPosts.value = true;
    } else{
        isPosts.value = false
    }
  },
  { deep: true }
);
</script>

<style scoped></style>
