<template>
  <el-dialog
    class="paragraph-source responsive-dialog"
    :title="$t('chat.paragraphSource.title')"
    v-model="dialogVisible"
    destroy-on-close
    append-to-body
    align-center
    :close-on-click-modal="false"
    :close-on-press-escape="false"
  >
    <div class="mb-8">
      <el-scrollbar>
        <div class="paragraph-source-height p-16 pb-0">
          <el-form label-position="top">
            <el-form-item :label="$t('chat.paragraphSource.question')">
              <el-input v-model="detail.problem_text" disabled />
            </el-form-item>
            <el-form-item :label="$t('chat.paragraphSource.optimizationQuestion')">
              <el-input v-model="detail.padding_problem_text" disabled />
            </el-form-item>
            <el-form-item :label="$t('chat.KnowledgeSource.referenceParagraph')">
              <div v-if="detail.paragraph_list.length > 0" class="w-full">
                <template v-for="(item, index) in detail.paragraph_list" :key="index">
                  <ParagraphCard :data="item" :content="item.content" :index="index" />
                </template>
              </div>
              <span v-else> - </span>
            </el-form-item>
          </el-form>
        </div>
      </el-scrollbar>
    </div>
  </el-dialog>
</template>
<script setup lang="ts">
import { ref, watch, onBeforeUnmount } from 'vue'
import { cloneDeep } from 'lodash'
import { arraySort } from '@/utils/utils'
import ParagraphCard from './component/ParagraphCard.vue'
const emit = defineEmits(['refresh'])

const dialogVisible = ref(false)
const detail = ref<any>({})

watch(dialogVisible, (bool) => {
  if (!bool) {
    detail.value = {}
  }
})

const open = (data: any, id?: string) => {
  detail.value = cloneDeep(data)
  detail.value.paragraph_list = id
    ? detail.value.paragraph_list.filter((v: any) => v.dataset_id === id)
    : detail.value.paragraph_list
  detail.value.paragraph_list = arraySort(detail.value.paragraph_list, 'similarity', true)
  dialogVisible.value = true
}
onBeforeUnmount(() => {
  dialogVisible.value = false
})
defineExpose({ open })
</script>
<style lang="scss">
.paragraph-source {
  padding: 0;

  .el-dialog__header {
    padding: 24px 24px 0 24px;
  }
  .el-dialog__body {
    padding: 8px !important;
  }
  .paragraph-source-height {
    max-height: calc(100vh - 260px);
  }
}
</style>
