<script setup lang="ts">
import { useI18n } from 'vue-i18n'

const { t } = useI18n()
</script>

<template>
  <div>{{ t('errors.not-exist') }}</div>
</template>

<route lang="yaml">
meta:
  layout: 404
</route>
