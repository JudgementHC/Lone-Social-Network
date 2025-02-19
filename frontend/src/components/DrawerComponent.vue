<script setup lang='ts'>
import { getCurrentInstance, onMounted, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { drawer, getAsset, links, user } from '~/logic'

const instance = getCurrentInstance()
const { t } = useI18n()

const imageSrc = ref('')

watch(user, () => {
  setImage()
})

onMounted(() => {
  setImage()
})

function setImage() {
  imageSrc.value = getAsset((user.value?.avatar.id || ''), instance)
}
</script>

<template>
  <nav
    :class="drawer ? 'translate-x-0' : '-translate-x-full'"
    class="fixed z-20 left-0 top-0 w-7/12 sm:w-64 min-h-full py-12 bg-cool-gray-50 dark:bg-cool-gray-800 overflow-scroll transition-all duration-300 ease-in-out transform"
  >
    <div v-if="user" class="relative flex flex-wrap justify-between mt-8 pb-6 px-4 border-b-1">
      <div class="mr-4 mb-4">
        <img :src="imageSrc" class="mx-auto w-20 h-20 rounded-full" />
      </div>

      <div class="flex justify-between flex-grow items-center w-1/2">
        <span class="font-semibold text-white">{{ user?.first_name }} {{ user?.last_name }}</span>

        <router-link class="focus:outline-none" :to="{ name: 'user-settings' }">
          <tabler:settings></tabler:settings>
        </router-link>
      </div>
    </div>

    <div class="my-4 px-4">
      <ul>
        <router-link
          v-for="(link, index) in links"
          :key="index"
          tabindex="-1"
          class="block mb-2 px-4 py-4 transition-colors duration dark:text-gray-100 hover:bg-gray-200 hover:text-black dark:hover:bg-gray-300 hover:font-bold rounded rounded-lg"
          tag="li"
          :to="link.href"
        >
          {{ t(link.title) }}
        </router-link>
      </ul>
    </div>
  </nav>

  <transition
    enter-from-class="opacity-0"
    enter-active-class="ease-out transition-medium"
    enter-to-class="opacity-100"
    leave-class="opacity-100"
    leave-active-class="ease-out transition-medium"
    leave-to-class="opacity-0"
  >
    <div v-show="drawer" class="fixed inset-0 z-10 transition-opacity cursor-pointer">
      <div class="absolute inset-0 bg-black opacity-50" tabindex="0" @click="drawer = false" />
    </div>
  </transition>
</template>
