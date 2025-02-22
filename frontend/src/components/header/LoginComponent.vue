<script setup lang='ts'>
import type { Emitter } from 'mitt'
import { useVuelidate } from '@vuelidate/core'
import { email, helpers, minLength, required } from '@vuelidate/validators'
import { getCurrentInstance, onMounted, reactive, ref, toRef } from 'vue'
import { useI18n } from 'vue-i18n'
import { DashAuthService } from '~/logic'

const { t } = useI18n()

const instance = getCurrentInstance()
const emitter = instance?.appContext.config.globalProperties.$emitter as Emitter<any>

const dashAuthService = new DashAuthService(instance)

const form = reactive({
  email: '',
  password: '',
})

const isProblem = ref(false)

const formRules = {
  email: {
    required: helpers.withMessage(t('validation.required-value'), required),
    email: helpers.withMessage(t('validation.non-correct-email'), email),
  },

  password: {
    required: helpers.withMessage(t('validation.required-value'), required),
    min: helpers.withMessage(t('validation.min-password-length', { length: 6 }), minLength(6)),
  },
}

const validation = useVuelidate(formRules, {
  email: toRef(form, 'email'),
  password: toRef(form, 'password'),
})

const onSubmit = async() => {
  validation.value.$touch()
  isProblem.value = false

  if (validation.value.$invalid)
    return false

  const { email, password } = form

  isProblem.value = await dashAuthService.login({
    email,
    password,
  })

  if (!isProblem.value)
    emitter.emit('hide:loginWindow')
}

const loginInput = ref<HTMLElement>()

onMounted(() => {
  loginInput.value?.focus()
})
</script>

<template>
  <form class="px-4" novalidate @submit.prevent="onSubmit">
    <h2 v-if="isProblem" class="mb-4 text-red-400">
      {{ t('validation.non-correct-data') }}
    </h2>

    <div class="mb-2">
      <label class="block text-sm font-normal mb-2" :for="`email-${instance?.uid}`">{{ t('email') }}</label>

      <input
        :id="`email-${instance?.uid}`"
        ref="loginInput"
        v-model="form.email"
        class="shadow ring-warm-gray-400 appearance-none border rounded w-full py-2 px-3 text-black leading-tight transition focus:outline-none focus:ring"
        name="email"
        type="email"
        required
        @input="validation?.email.$reset()"
      />

      <p :class="{ 'invisible': !validation?.email?.$errors[0]?.$message }">
        <small class="text-red-400">{{ validation?.email?.$errors[0]?.$message || 'hide' }}</small>
      </p>
    </div>

    <div class="mb-4">
      <label
        class="block text-sm font-normal mb-2"
        :for="`password-${instance?.uid}`"
      >{{ t('password') }}</label>
      <input
        :id="`password-${instance?.uid}`"
        v-model="form.password"
        class="shadow ring-warm-gray-400 appearance-none border rounded w-full py-2 px-3 text-black leading-tight transition focus:outline-none focus:ring"
        type="password"
        name="password"
        required
        @focus="validation?.email.$touch()"
        @blur="validation?.password.$touch()"
        @input="validation?.password.$reset()"
      />

      <p :class="{ 'invisible': !validation?.password?.$errors[0]?.$message }">
        <small class="text-red-400">{{ validation?.password?.$errors[0]?.$message || 'hide' }}</small>
      </p>
    </div>

    <div class="flex items-center justify-between">
      <button
        class="px-4 py-2 rounded text-white inline-block shadow-lg bg-blue-500 transition hover:bg-blue-600 focus:bg-blue-700 focus:outline-none"
        type="submit"
      >
        {{ t('login.sign-in') }}
      </button>
      <a
        class="inline-block align-baseline font-normal text-sm text-blue-500 hover:text-blue-800"
        href="#"
      >{{ t('login.forgot-password') }}</a>
    </div>
  </form>
</template>
