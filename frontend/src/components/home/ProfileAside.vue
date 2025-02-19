<script setup lang='ts'>
import { isEmpty } from 'lodash'
import { getCurrentInstance, onBeforeMount, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { user, imageLoader, getAsset, home } from '~/logic'

const { t } = useI18n()

const imageSrc = ref('')

const instance = getCurrentInstance()

watch(home, () => {
  initValues()
})

onBeforeMount(() => {
  if (!isEmpty(home.value))
    initValues()
})

const fileInput = ref<HTMLElement>()

const onFileChange = (progressEvent: ProgressEvent<FileReader>) => {
  // progressEvent.target?.result
}

function initValues() {
  imageSrc.value = getAsset((home.value?.admin_info.avatar.id || ''), instance)
}
</script>

<template>
  <div
    class="lg:sticky top-44 left-0 dark:bg-cool-gray-700 shadow-2xl rounded-md transform -translate-y-5 sm:-translate-y-10 lg:transform-none"
  >
    <div class="profile-aside__block group border-cool-gray-50 dark:border-cool-gray-700">
      <div
        v-if="imageSrc && user?.role.admin_access"
        class="absolute top-0 left-0 flex justify-center items-center w-full h-full bg-black opacity-0 cursor-pointer transition duration group-hover:opacity-60 bg--default"
        @click="fileInput?.click()"
      >
        <mdi:camera class="text-3xl"></mdi:camera>
      </div>

      <ImageLoader v-if="!imageSrc" size="text-2xl"></ImageLoader>

      <img :src="imageSrc" />
    </div>

    <input ref="fileInput" class="hidden" type="file" @change="imageLoader($event, onFileChange)" />

    <div class="px-2 py-4 text-center text-lg">
      <p>{{ t('about.fullname') }}</p>
    </div>

    <SocialLinks></SocialLinks>
  </div>
</template>

<style lang="postcss">
.profile-aside {
  &__block {
    @apply relative w-36 h-36 mx-auto -mb-20 rounded-full border-5 transform -translate-y-20 overflow-hidden;

    @screen sm {
      @apply w-48 h-48 -mb-24 -translate-y-24;
    }

    @screen lg {
      @apply w-52 h-52 -mb-28 -translate-y-28;
    }
  }
}
</style>
