# Dialog Patterns: Form Dialog

Standard form dialog for create/edit operations.

## Template

```vue
<template>
  <el-dialog
    v-model="dialogVisible"
    :title="isEdit ? '编辑' : '新增'"
    width="560px"
    destroy-on-close
    @closed="resetForm"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="100px"
      label-position="right"
    >
      <el-form-item label="名称" prop="name">
        <el-input v-model="form.name" placeholder="请输入名称" maxlength="64" show-word-limit />
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-select v-model="form.status" placeholder="请选择">
          <el-option label="启用" value="ACTIVE" />
          <el-option label="禁用" value="DISABLED" />
        </el-select>
      </el-form-item>
      <el-form-item label="描述" prop="description">
        <el-input v-model="form.description" type="textarea" :rows="3" placeholder="请输入描述" />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="dialogVisible = false">取消</el-button>
      <el-button type="primary" :loading="submitting" @click="handleSubmit">确定</el-button>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref, reactive, nextTick } from 'vue'
import type { FormInstance, FormRules } from 'element-plus'
import { ElMessage } from 'element-plus'

interface FormData {
  name: string
  status: string
  description: string
}

const emit = defineEmits<{
  success: []
}>()

const dialogVisible = ref(false)
const isEdit = ref(false)
const submitting = ref(false)
const editId = ref('')
const formRef = ref<FormInstance>()

const form = reactive<FormData>({
  name: '',
  status: 'ACTIVE',
  description: ''
})

const rules: FormRules<FormData> = {
  name: [
    { required: true, message: '请输入名称', trigger: 'blur' },
    { min: 2, max: 64, message: '长度 2-64 个字符', trigger: 'blur' }
  ],
  status: [
    { required: true, message: '请选择状态', trigger: 'change' }
  ]
}

function open(data?: FormData & { id: string }) {
  dialogVisible.value = true
  if (data) {
    isEdit.value = true
    editId.value = data.id
    nextTick(() => {
      Object.assign(form, {
        name: data.name,
        status: data.status,
        description: data.description
      })
    })
  } else {
    isEdit.value = false
    editId.value = ''
  }
}

function resetForm() {
  formRef.value?.resetFields()
  Object.assign(form, { name: '', status: 'ACTIVE', description: '' })
}

async function handleSubmit() {
  const valid = await formRef.value?.validate().catch(() => false)
  if (!valid) return

  submitting.value = true
  try {
    if (isEdit.value) {
      // await updateApi(editId.value, form)
      ElMessage.success('更新成功')
    } else {
      // await createApi(form)
      ElMessage.success('创建成功')
    }
    dialogVisible.value = false
    emit('success')
  } finally {
    submitting.value = false
  }
}

defineExpose({ open })
</script>
```

## Key Points

- Use `destroy-on-close` to reset dialog state
- Use `@closed` instead of `@close` for cleanup (after animation)
- Use `defineExpose({ open })` pattern for parent to call
- Use `FormRules<T>` for type-safe validation rules
- Use `:loading` on submit button to prevent double-click
- Use `nextTick` when populating form after open
