<template>
  <el-card class="simple-card" shadow="hover" v-loading="form.loading">
    <template #header>
      <div class="card-header">
        <span>ALWAYS_USE_RELAY</span>
      </div>
    </template>
    <el-form :disabled="!canSend">
      <el-form-item>
        <el-switch v-model="form.option" active-value="Y" inactive-value="N"></el-switch>
      </el-form-item>
      <el-form-item>
        <el-button @click="get">{{ T('Refresh') }}</el-button>
        <el-button @click="save" type="primary">{{ T('Save') }}</el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>
<script setup>

  import { T } from '@/utils/i18n'
  import { reactive, watch } from 'vue'
  import { sendCmd } from '@/api/rustdesk'
  import { ElMessage } from 'element-plus'
  import { ID_TARGET } from '@/views/rustdesk/options'

  const emits = defineEmits('success')
  const props = defineProps({
    canSend: Boolean,
  })

  const form = reactive({
    cmd: 'aur',
    option: '',
    target: ID_TARGET,
    value: 0,
    loading: false,
  })
  const get = async () => {
    form.loading = true
    const res = await sendCmd({ cmd: 'aur', target: ID_TARGET }).catch(_ => false)
    form.loading = false
    if (res) {
      if (res.data === 'ALWAYS_USE_RELAY: true' || res.data === 'ALWAYS_USE_RELAY: true\n') {
        form.option = 'Y'
      } else {
        form.option = 'N'
      }
    }
  }
  const save = async () => {
    const res = await sendCmd(form).catch(_ => false)
    if (res) {
      ElMessage.success(T('OperationSuccess'))
      emits('success')
    }
  }

  watch(() => props.canSend, (v) => {
    if (v) {
      get()
    }
  })
</script>


<style scoped lang="scss">

</style>
